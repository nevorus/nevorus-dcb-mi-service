# =============================================================
# NAKOPAY – Micro-Intégrateur DCB
# Dockerfile – deployment/docker/Dockerfile
# =============================================================

# ── Stage 1 : Build Maven + Java 11 ────────────────────────
FROM maven:3.9.11-eclipse-temurin-11 AS builder

WORKDIR /build

# Cache des dependances Maven (cette couche est cachee tant que le pom ne change pas)
COPY pom.xml .
RUN mvn dependency:go-offline -q 2>/dev/null || true

# Sources du projet
COPY artifact.xml .
COPY src/ src/

# Build -> genere le .car
RUN mvn clean install -DskipTests

# ── Stage 2 : Runtime WSO2 MI 4.4.0 ────────────────────────
FROM wso2/wso2mi:4.4.0

# Deployer le .car (wildcard pour ne pas hardcoder le nom/version)
COPY --from=builder /build/target/*.car \
     ${WSO2_SERVER_HOME}/repository/deployment/server/carbonapps/

# deployment.toml
COPY deployment/docker/deployment.toml \
     ${WSO2_SERVER_HOME}/conf/deployment.toml

EXPOSE 8290 8253 9164 9201
