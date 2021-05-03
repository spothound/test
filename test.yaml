apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: dbapp
  name: dbapp
  namespace: test
spec:
  replicas: 4
  selector:
    matchLabels:
      app: dbapp
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 75%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: dbapp
    spec:
      containers:
      - command: ["python"]
        args: ["main.py"]
        env:
        - name: ENVIRONMENT
          value: test
        - name: DATABASE_HOST
          value: psql.internal:5432
        - name: DATABASE_PASSWORD
          value: "12345"
        image: registry.hub.docker.com/applibrary/dbapp:latest
        imagePullPolicy: IfNotPresent
        livenessProbe:
          failureThreshold: 25
          initialDelaySeconds: 60
          periodSeconds: 30
          successThreshold: 1
          tcpSocket:
            port: 80
          timeoutSeconds: 1
        name: dbapp
        ports:
        - containerPort: 80
          protocol: TCP
        readinessProbe:
          failureThreshold: 3
          initialDelaySeconds: 60
          periodSeconds: 10
          successThreshold: 1
          tcpSocket:
            port: 80
          timeoutSeconds: 1
        resources:
          limits:
            cpu: "1"
            memory: 200Mi
          requests:
            cpu: "1"
            memory: 100Mi
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /api-keys/
          name: api-keys
          readOnly: true
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      volumes:
      - name: api-keys
        configMap:
          defaultMode: 420
          items:
          - key: private.pem
            path: private.pem
          - key: public.pem
            path: public.pem
          name: api-keys
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: dbapp
  name: dbapp
  namespace: test
spec:
  externalTrafficPolicy: Cluster
  ports:
  - port: 8888
    protocol: TCP
    targetPort: 80
  selector:
    app: dbapp
  sessionAffinity: None
  type: LoadBalancer
