# =============================================================
# NAKOPAY – Micro-Intégrateur DCB
# Dockerfile – deployment/docker/Dockerfile
# Structure identique aux projets internes NEVORUS/BRASTORNE
# =============================================================

# ── Stage 1 : Build Maven + Java 11 ──────────────────────────
FROM maven:3.9.11-eclipse-temurin-11 AS builder

WORKDIR /build

# Cache des dépendances Maven
COPY pom.xml .
RUN mvn dependency:go-offline -q 2>/dev/null || true

# Sources du projet
COPY artifact.xml .
COPY src/ src/

# Build → génère le .car
RUN mvn clean install -DskipTests

# Afficher le contenu du .car pour vérification au build
RUN echo "=== CONTENU .CAR ===" && \
    jar tf target/nakopay-mi-v8_1.0.0.car && \
    echo "=== FIN .CAR ==="

# ── Stage 2 : Runtime WSO2 MI 4.4.0 ──────────────────────────
FROM wso2/wso2mi:4.4.0

# Déployer le .car
COPY --from=builder \
     /build/target/nakopay-mi-v8_1.0.0.car \
     ${WSO2_SERVER_HOME}/repository/deployment/server/carbonapps/

# deployment.toml – configuration princmpale (system.parameter inclus)
COPY deployment/docker/deployment.toml \
     ${WSO2_SERVER_HOME}/conf/deployment.toml

# Keystores (optionnel si pas de TLS custom)
# COPY deployment/docker/resources/wso2carbon.jks \
#      ${WSO2_SERVER_HOME}/repository/resources/security/wso2carbon.jks
# COPY deployment/docker/resources/client-truststore.jks \
#      ${WSO2_SERVER_HOME}/repository/resources/security/client-truststore.jks

EXPOSE 8290 8253 9164 9201
