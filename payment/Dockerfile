FROM python:3.11-alpine3.22 AS builder
RUN apk update && \
    apk add --no-cache musl-dev && \
    apk add --no-cache libffi-dev && \
    apk add --no-cache openssl-dev && \
    apk add --no-cache gcc && \
    apk add --no-cache python3-dev && \
    apk add --no-cache uwsgi uwsgi-python3
WORKDIR /app
COPY requirements.txt .
RUN pip3 install --prefix=/install -r requirements.txt
COPY . .

FROM python:3.11-alpine3.22
WORKDIR /app
COPY --from=builder /install /usr/local
COPY --from=builder /app /app
RUN addgroup -S roboshop && \
    adduser -S roboshop -G roboshop && \
    chown -R roboshop:roboshop /app
ENV CART_HOST="cart-cont" \
    CART_PORT="8080" \
    USER_HOST="user-cont" \
    USER_PORT="8080" \
    AMQP_HOST="rabbitmq-cont" \
    AMQP_USER="roboshop" \
    AMQP_PASS="roboshop123"
EXPOSE 8080
USER roboshop
ENTRYPOINT ["uwsgi", "--ini"]
CMD ["payment.ini"]
