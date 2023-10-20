# Set AWS credentials as environment variables:
export AWS_ACCESS_KEY_ID=************
export AWS_SECRET_ACCESS_KEY=**************
# Spin up the two containers and a network for them to communciate on:
docker network create terranet
docker network create terraboard
docker run --name db \
  -e POSTGRES_USER=gorm \
  -e POSTGRES_DB=gorm \
  -e POSTGRES_PASSWORD="mypassword" \
  -e GODEBUG="netdns=go" \
  --net terraboard \
  --detach \
  --restart=always \
  postgres:9.5


docker run -p 8080:8080 \
  -e AWS_ACCESS_KEY_ID="${AWS_ACCESS_KEY_ID}" \
  -e AWS_SECRET_ACCESS_KEY="${AWS_SECRET_ACCESS_KEY}" \
  -e AWS_REGION="${AWS_DEFAULT_REGION}" \
  -e AWS_BUCKET="${AWS_BUCKET}" \
  -e WS_DYNAMODB_TABLE="${AWS_DYNAMODB_TABLE}" \
  -e DB_PASSWORD="mypassword" \
  -e DB_SSLMODE="disable" \
  --net terraboard \
  camptocamp/terraboard:latest
