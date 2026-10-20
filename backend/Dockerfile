# ---- ビルド用 ----
FROM eclipse-temurin:25-jdk AS build
WORKDIR /src
# 先に pom.xml だけコピーして依存を取得する（ソースだけ変えたときはキャッシュが効く）
COPY mvnw pom.xml ./
COPY .mvn .mvn
RUN ./mvnw --batch-mode -q dependency:go-offline
COPY src src
# テストは CI（backend-tests.yml）で実行済みなのでここでは省略する
RUN ./mvnw --batch-mode -q package -DskipTests

# ---- 実行用（JRE だけの小さいイメージ）----
FROM eclipse-temurin:25-jre
WORKDIR /app
# root で動かさない。logs/app.log を書き込めるよう /app の所有者も変える
RUN useradd --system app && chown app /app
USER app
COPY --from=build /src/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
