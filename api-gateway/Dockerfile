# Первая стадия: кэширование зависимостей
FROM eclipse-temurin:17-jdk AS deps
WORKDIR /app

# Копируем только pom.xml и mvnw
COPY pom.xml .
COPY .mvn/ .mvn
COPY mvnw .
RUN chmod +x mvnw

# Скачиваем зависимости (кэшируется)
RUN ./mvnw dependency:go-offline -B

# Вторая стадия: сборка приложения
FROM eclipse-temurin:17-jdk AS builder
WORKDIR /app
COPY --from=deps /root/.m2 /root/.m2
COPY --from=deps /app/mvnw .
COPY . .
RUN chmod +x mvnw
RUN ./mvnw clean package -DskipTests

# Финальная стадия: рантайм
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]