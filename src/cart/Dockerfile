# Build Stage
FROM maven:3.8-amazoncorretto-17 as build-env

ARG JAR_PATH

VOLUME /tmp
WORKDIR /

COPY ./pom.xml .

RUN mvn dependency:go-offline -B

COPY ./src ./src

RUN mvn -DskipTests package
RUN mv /$JAR_PATH /app.jar

# Package Stage
FROM amazoncorretto:17-al2-full

RUN yum install -y \
    shadow-utils && \
    yum clean all

ENV APPUSER=appuser
ENV APPUID=1000
ENV APPGID=1000

RUN useradd \
    --home "/app" \
    --create-home \
    --user-group \
    --uid "$APPUID" \
    "$APPUSER"

WORKDIR /app
USER appuser

COPY --chown=appuser:appuser --from=build-env /app.jar .

EXPOSE 8080

CMD exec java $JAVA_OPTS -jar /app/app.jar
