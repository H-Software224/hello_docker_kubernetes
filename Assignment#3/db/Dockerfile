# Dockerfile

# MySQL 이미지를 기반으로 이미지 생성
FROM mysql:8

# MySQL 설정
ENV MYSQL_ROOT_PASSWORD=secret
ENV MYSQL_DATABASE=todos
ENV MYSQL_PASSWORD=secret

VOLUME [/var/lib/mysql]
# 포트 설정 (기본 MySQL 포트는 3306)
EXPOSE 3306