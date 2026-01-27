
---

## 🔗 Common

|Key|Typical Values|What it does|
|---|---|---|
|`spring.kafka.bootstrap-servers`|`localhost:9092`|Where Kafka broker lives|
|`spring.kafka.client-id`|`my-app`|Identifies your app in Kafka logs|
|`spring.kafka.properties.security.protocol`|`PLAINTEXT` / `SASL_SSL`|Security mode|

---

## 🚀 Producer Config

|Key|Common Values|Meaning (real talk)|
|---|---|---|
|`spring.kafka.producer.acks`|`0` `1` `all`|When Kafka confirms write|
||`all` (best)|waits for replicas|
|`spring.kafka.producer.retries`|`0` to `Integer.MAX`|retry failed sends|
|`spring.kafka.producer.properties.enable.idempotence`|`true/false`|avoids duplicates on retry|
|`spring.kafka.producer.key-serializer`|StringSerializer|serialize key|
|`spring.kafka.producer.value-serializer`|JsonSerializer|serialize payload|
|`spring.kafka.producer.batch-size`|bytes (16384 typical)|batch messages for performance|
|`spring.kafka.producer.linger-ms`|0–20|wait to batch more msgs|

👉 **Golden setup** usually:

```
acks=all
enable.idempotence=true
retries=large
```

---

## 📥 Consumer Config

|Key|Common Values|Meaning|
|---|---|---|
|`spring.kafka.consumer.group-id`|`my-group`|consumer group name|
|`spring.kafka.consumer.auto-offset-reset`|`earliest` `latest`|start position|
|`spring.kafka.consumer.enable-auto-commit`|`true/false`|auto commit offsets|
||❗false recommended|manual control|
|`spring.kafka.consumer.key-deserializer`|StringDeserializer|read key|
|`spring.kafka.consumer.value-deserializer`|JsonDeserializer|read payload|
|`spring.kafka.consumer.max-poll-records`|10–500|batch size per poll|
|`spring.kafka.consumer.fetch-max-wait`|ms|wait for data|

---

## ✅ Acknowledgment Mode (Spring specific)

This controls when offset commits happen.

|Key|Values|Meaning|
|---|---|---|
|`spring.kafka.listener.ack-mode`|`RECORD`|commit after each msg|
||`BATCH`|after batch|
||`MANUAL`|you call ack|
||`MANUAL_IMMEDIATE`|instant commit|

👉 Best for control:

```
MANUAL or RECORD
```

---

# 🏆 Simple Safe Starter YAML (realistic)

Mentally this:

```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092

    producer:
      acks: all
      retries: 10
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer
      properties:
        enable.idempotence: true

    consumer:
      group-id: demo-group
      auto-offset-reset: earliest
      enable-auto-commit: false
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.springframework.kafka.support.serializer.JsonDeserializer

    listener:
      ack-mode: manual
```

This alone already gives:

✔ safe produce  
✔ controlled consume  
✔ retry capable  
✔ no message loss

---

# 🧠 Important mental mapping

### Producer retries ≠ Consumer retries

Producer retry = broker/network fail  
Consumer retry = your business logic failed

Different problems.

---

# ⚠️ Common newbie mistakes (avoid these)

❌ auto-commit true  
❌ acks=1 in prod  
❌ no idempotence  
❌ single partition topics  
❌ no DLQ

---
