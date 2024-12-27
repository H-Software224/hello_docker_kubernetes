# hello_docker_kubernetes

## 프로젝트 목적과 설명
안녕하세요 Docker and Kubernetes를 통해 한 프로젝트를 만들어봤습니다. 세가지 폴더가 있습니다.<br>

- **Assignment#1**: Dockerfile을 통해서 docker-image을 이용하여서 창에 띄우는 CI/CD 사용<br>
![image](./image/docker.jpg)<br>
  - dockerfile 설명
    ```
    # syntax=docker/dockerfile:1

    FROM node:18-alpine # node:18-alpine image 이용
    WORKDIR /app # app 폴더에 작업
    COPY . . # 상위 폴더에 COPY
    RUN yarn install --production # yarn 명령어를 통해서 yarn.lock 파일을 압축 풀고 실행시킨다.
    CMD ["node", "src/index.js"] # node.js를 이용하여서 index.js를 구동시킨다.
    EXPOSE 3000 # PORT 번호를 3000으로 한다.
    ```
- **Assignment#2**: Docker-compose를 통해서 yml 파일로 생성하여서 두개 이상의 node을 띄워서 CI/CD 사용<br>
  - docker-compose.yml
    ```
    version: '3.8'
    services:
      app:
        image: computerdreams/getting-started2:node
        command: sh -c "yarn install && yarn run dev"
        ports:
          - 3010:3000
        working_dir: /app
        volumes:
          - ./:/app
        environment:
          MYSQL_HOST: mysql
          MYSQL_USER: root
          MYSQL_PASSWORD: secret
          MYSQL_DB: todos
      mysql:
        image: computerdreams/getting-started2:mysql
        volumes:
          - doit:/var/lib/mysql
        environment:
          MYSQL_ROOT_PASSWORD: secret
          MYSQL_DATABASE: todos
    volumes:
      doit:
    ```
+ **Assignment#3**: Kubernetes를 이용하여 **Assignment#2** 폴더 내용을 참고하여서 docker->kubernetes로 변환시킵니다.<br>
![image](./image/kubernetes.jpg)<br>
  - kubernetes를 이용하여 yml로 변환<br>
    + node.js 이용<br>
    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: web
      namespace: default
      labels:
        app: web
    spec:
      type: NodePort
      ports:
        - port: 3001
          targetPort: 3000
          nodePort: 30001
      selector:
        app: web
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: node
      namespace: default
      labels:
        app: web
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: web
      template:
        metadata:
          labels:
            app: web
        spec:
          containers:
            - image: computerdreams/k8s:web
              name: web
              env:
              - name: MYSQL_ROOT_PASSWORD
                value: root
              - name: MYSQL_DATABASE
                value: todos
    ```
    + mysql을 이용
    ```
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql-sv
      labels:
         db: database
    spec:
      selector:
        db: database
      ports:
        - port: 3306
      clusterIP: None
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: mysql
      namespace: default
      labels:
        db: database
    spec:
      replicas: 1
      selector:
        matchLabels:
          db: database
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            db: database
        spec:
          containers:
            - image: computerdreams/k8s:mysql
              name: mysql-db
    ```

## 참고문헌
참고문헌은 [docker-getting-started](https://github.com/docker/getting-started-app), [docker 홈페이지](https://docs.docker.com/get-started/), [kubernetes 홈페이지](https://kubernetes.io/docs/home/)
에서 참고하였습니다. 
