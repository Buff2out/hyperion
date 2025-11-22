psd2

ngrok

---

POST
/auth/bank-token
```bash
curl -X 'POST' \
  'https://vbank.open.bankingapi.ru/auth/bank-token?client_id=team275&client_secret=super6ecretid' \
  -H 'accept: application/json' \
  -d ''
```

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJ0ZWFtMjc1IiwiY2xpZW50X2lkIjoidGVhbTI3NSIsInR5cGUiOiJ0ZWFtIiwiaXNzIjoidmJhbmsiLCJhdWQiOiJvcGVuYmFua2luZyIsImV4cCI6MTc2MjUyMjM0MX0.5VNzRnhXnkTA5IkJok-9L-zDGYQSrRmFkGAyw2FM4k0",
  "token_type": "bearer",
  "client_id": "team275",
  "algorithm": "HS256",
  "expires_in": 86400
}
```


---
POST
/account-consents/request
```bash
curl -X 'POST' \
  'https://vbank.open.bankingapi.ru/account-consents/request' \
  -H 'accept: application/json' \
  -H 'x-requesting-bank: team275' \
  -H 'Content-Type: application/json' \
  -d '{
  "client_id": "team275-6",
  "permissions": [
    "ReadAccountsDetail",
    "ReadBalances",
    "ReadTransactionsDetail"
  ],
  "reason": "",
  "requesting_bank": "test_bank",
  "requesting_bank_name": "Test Bank"
}'
```
json response:
```json
{
  "request_id": "req-73fdb8b9d63e",
  "consent_id": "consent-46965b4ef205",
  "status": "approved",
  "message": "Согласие одобрено автоматически",
  "created_at": "2025-11-06T13:21:06.556824",
  "auto_approved": true
}
```

---
GET
/account-consents/{consent_id}
```bash
curl -X 'GET' \
  'https://vbank.open.bankingapi.ru/account-consents/consent-46965b4ef205' \
  -H 'accept: application/json' \
  -H 'x-fapi-interaction-id: team275-6'
```

```json
{
  "data": {
    "consentId": "consent-46965b4ef205",
    "status": "Authorized",
    "creationDateTime": "2025-11-06T13:21:06.557680Z",
    "statusUpdateDateTime": "2025-11-06T13:21:06.557680Z",
    "permissions": [
      "ReadAccountsDetail",
      "ReadBalances",
      "ReadTransactionsDetail"
    ],
    "expirationDateTime": "2026-11-06T13:21:06.557675Z"
  },
  "links": {
    "self": "/account-consents/consent-46965b4ef205"
  },
  "meta": {}
}
```
---
DELETE
/account-consents/{consent_id}
```bash
curl -X 'DELETE' \
  'https://vbank.open.bankingapi.ru/account-consents/consent-46965b4ef205' \
  -H 'accept: */*' \
  -H 'x-fapi-interaction-id: team275-6'
```

204
 access-control-allow-credentials: true access-control-allow-origin: https://vbank.open.bankingapi.ru  content-security-policy: upgrade-insecure-requests  date: Thu,06 Nov 2025 13:30:50 GMT  server: nginx/1.29.2  strict-transport-security: max-age=63072000; includeSubDomains; preload vary: Origin

---

GET
/accounts

```bash
curl -X 'GET' \
  'https://vbank.open.bankingapi.ru/accounts?client_id=team275-6' \
  -H 'accept: application/json' \
  -H 'x-consent-id: consent-46965b4ef205' \
  -H 'x-requesting-bank: team275'
```

```json
{
  "data": {
    "account": [
      {
        "accountId": "acc-3846",
        "status": "Enabled",
        "currency": "RUB",
        "accountType": "Personal",
        "accountSubType": "Checking",
        "nickname": "Checking счет",
        "openingDate": "2024-10-30",
        "account": [
          {
            "schemeName": "RU.CBR.PAN",
            "identification": "4081781027506084636",
            "name": "Пенсионеров Пенсионер Пенсионерович (team275)"
          }
        ]
      }
    ]
  },
  "links": {
    "self": "/accounts"
  },
  "meta": {
    "totalPages": 1
  }
}
```

---

```bash
curl -X 'GET' \
  'https://vbank.open.bankingapi.ru/accounts/acc-3846' \
  -H 'accept: application/json' \
  -H 'x-consent-id: consent-46965b4ef205' \
  -H 'x-requesting-bank: team275'
```

```json
{
  "data": {
    "account": [
      {
        "accountId": "acc-3846",
        "status": "Enabled",
        "currency": "RUB",
        "accountType": "Personal",
        "accountSubType": "Checking",
        "description": "checking account",
        "nickname": "Checking счет",
        "openingDate": "2024-10-30"
      }
    ]
  }
}
```

---

```bash
curl -X 'GET' \
  'https://vbank.open.bankingapi.ru/accounts/acc-3846/balances' \
  -H 'accept: application/json' \
  -H 'x-consent-id: consent-46965b4ef205' \
  -H 'x-requesting-bank: team275'
```

```json
{
  "data": {
    "balance": [
      {
        "accountId": "acc-3846",
        "type": "InterimAvailable",
        "dateTime": "2025-11-06T14:50:59.151865Z",
        "amount": {
          "amount": "121398.37",
          "currency": "RUB"
        },
        "creditDebitIndicator": "Credit"
      },
      {
        "accountId": "acc-3846",
        "type": "InterimBooked",
        "dateTime": "2025-11-06T14:50:59.151876Z",
        "amount": {
          "amount": "121398.37",
          "currency": "RUB"
        },
        "creditDebitIndicator": "Credit"
      }
    ]
  }
}
```

---

```bash
curl -X 'GET' \
  'https://vbank.open.bankingapi.ru/accounts/acc-3846/transactions?from_booking_date_time=2025-01-01T00%3A00%3A00Z&to_booking_date_time=2025-12-31T23%3A59%3A59Z&page=1&limit=3' \
  -H 'accept: application/json' \
  -H 'x-consent-id: consent-46965b4ef205' \
  -H 'x-requesting-bank: team275'
```

```json
{
  "data": {
    "transaction": [
      {
        "accountId": "acc-3846",
        "transactionId": "tx-team275-6-m0-1",
        "amount": {
          "amount": "114911.38",
          "currency": "RUB"
        },
        "creditDebitIndicator": "Credit",
        "status": "Booked",
        "bookingDateTime": "2025-10-28T17:59:45.080562Z",
        "valueDateTime": "2025-10-28T17:59:45.080562Z",
        "transactionInformation": "💼 Зарплата",
        "bankTransactionCode": {
          "code": "ReceivedCreditTransfer"
        }
      },
      {
        "accountId": "acc-3846",
        "transactionId": "tx-team275-6-m0-5",
        "amount": {
          "amount": "8378.49",
          "currency": "RUB"
        },
        "creditDebitIndicator": "Debit",
        "status": "Booked",
        "bookingDateTime": "2025-10-25T17:59:45.080562Z",
        "valueDateTime": "2025-10-25T17:59:45.080562Z",
        "transactionInformation": "🎬 Развлечения/Покупки",
        "bankTransactionCode": {
          "code": "IssuedDebitTransfer"
        }
      },
      {
        "accountId": "acc-3846",
        "transactionId": "tx-team275-6-m0-4",
        "amount": {
          "amount": "2707.49",
          "currency": "RUB"
        },
        "creditDebitIndicator": "Debit",
        "status": "Booked",
        "bookingDateTime": "2025-10-22T17:59:45.080562Z",
        "valueDateTime": "2025-10-22T17:59:45.080562Z",
        "transactionInformation": "🚌 Транспорт",
        "bankTransactionCode": {
          "code": "IssuedDebitTransfer"
        }
      }
    ]
  },
  "links": {
    "self": "/accounts/acc-3846/transactions?page=1&limit=3",
    "next": "/accounts/acc-3846/transactions?page=2&limit=3"
  },
  "meta": {
    "totalPages": 24,
    "totalRecords": 70,
    "currentPage": 1,
    "pageSize": 3
  }
}
```

следующие 6 страниц:

```bash
curl -X 'GET' \
  'https://vbank.open.bankingapi.ru/accounts/acc-3846/transactions?from_booking_date_time=2025-01-01T00%3A00%3A00Z&to_booking_date_time=2025-12-31T23%3A59%3A59Z&page=2&limit=6' \
  -H 'accept: application/json' \
  -H 'x-consent-id: consent-46965b4ef205' \
  -H 'x-requesting-bank: team275'
```

```json
{
  "data": {
    "transaction": [
      {
        "accountId": "acc-3846",
        "transactionId": "tx-team275-6-m1-1",
        "amount": {
          "amount": "135275.00",
          "currency": "RUB"
        },
        "creditDebitIndicator": "Credit",
        "status": "Booked",
        "bookingDateTime": "2025-09-28T17:59:45.080562Z",
        "valueDateTime": "2025-09-28T17:59:45.080562Z",
        "transactionInformation": "💼 Зарплата",
        "bankTransactionCode": {
          "code": "ReceivedCreditTransfer"
        }
      },
      {
        "accountId": "acc-3846",
        "transactionId": "tx-team275-6-m1-5",
        "amount": {
          "amount": "7164.52",
          "currency": "RUB"
        },
        "creditDebitIndicator": "Debit",
        "status": "Booked",
        "bookingDateTime": "2025-09-25T17:59:45.080562Z",
        "valueDateTime": "2025-09-25T17:59:45.080562Z",
        "transactionInformation": "🎬 Развлечения/Покупки",
        "bankTransactionCode": {
          "code": "IssuedDebitTransfer"
        }
      },
      {
        "accountId": "acc-3846",
        "transactionId": "tx-team275-6-m1-4",
        "amount": {
          "amount": "3052.32",
          "currency": "RUB"
        },
        "creditDebitIndicator": "Debit",
        "status": "Booked",
        "bookingDateTime": "2025-09-22T17:59:45.080562Z",
        "valueDateTime": "2025-09-22T17:59:45.080562Z",
        "transactionInformation": "🚌 Транспорт",
        "bankTransactionCode": {
          "code": "IssuedDebitTransfer"
        }
      },
      {
        "accountId": "acc-3846",
        "transactionId": "tx-team275-6-m1-3",
        "amount": {
          "amount": "20870.88",
          "currency": "RUB"
        },
        "creditDebitIndicator": "Debit",
        "status": "Booked",
        "bookingDateTime": "2025-09-20T17:59:45.080562Z",
        "valueDateTime": "2025-09-20T17:59:45.080562Z",
        "transactionInformation": "🏠 ЖКХ/Аренда",
        "bankTransactionCode": {
          "code": "IssuedDebitTransfer"
        }
      },
      {
        "accountId": "acc-3846",
        "transactionId": "tx-team275-6-m1-6",
        "amount": {
          "amount": "22441.23",
          "currency": "RUB"
        },
        "creditDebitIndicator": "Credit",
        "status": "Booked",
        "bookingDateTime": "2025-09-18T17:59:45.080562Z",
        "valueDateTime": "2025-09-18T17:59:45.080562Z",
        "transactionInformation": "💰 Подработка/Бонус",
        "bankTransactionCode": {
          "code": "ReceivedCreditTransfer"
        }
      },
      {
        "accountId": "acc-3846",
        "transactionId": "tx-team275-6-m1-2",
        "amount": {
          "amount": "7699.96",
          "currency": "RUB"
        },
        "creditDebitIndicator": "Debit",
        "status": "Booked",
        "bookingDateTime": "2025-09-15T17:59:45.080562Z",
        "valueDateTime": "2025-09-15T17:59:45.080562Z",
        "transactionInformation": "🏪 Продукты",
        "bankTransactionCode": {
          "code": "IssuedDebitTransfer"
        }
      }
    ]
  },
  "links": {
    "self": "/accounts/acc-3846/transactions?page=2&limit=6",
    "next": "/accounts/acc-3846/transactions?page=3&limit=6",
    "prev": "/accounts/acc-3846/transactions?page=1&limit=6"
  },
  "meta": {
    "totalPages": 12,
    "totalRecords": 70,
    "currentPage": 2,
    "pageSize": 6
  }
}
```