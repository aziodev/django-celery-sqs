# Celery-SQS

A sample Django App that shows how to use Celery with Amazon SQS as the Broker.

## Quick how to
* clone this repo
* create a virtualenv & install requirements
```sh
$> mkvirtualenv -p /usr/local/bin/python3 celery-sqs
...
$> pip install -r requirements.txt
```
* Add `celerysqs/secret.py` with the following template
  * NOTE: the access/secret keys must have the IAM Policy listed below attached
```python
KEY = 'some secret key'

SQS = {
    'access_key': 'your aws_access_key',
    'secret_key': 'your aws_secret_key',
}
```
* run the server
```sh
$> DJANGO_SETTINGS_MODULE=celerysqs.conf.aws python manage.py runserver [ip:[port]]
```
* run the celery worker
```sh
$> DJANGO_SETTINGS_MODULE=celerysqs.conf.aws celery worker -A celerysqs --concurrency=1 -l info
```
* queue some tasks
```sh
$> ./scripts/curls.sh
```

## IAM Policy Requirements

### ListQueues (*)
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1234567890000",
            "Effect": "Allow",
            "Action": [
                "sqs:ListQueues"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

### CRUD (prefix-)
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1234567890000",
            "Effect": "Allow",
            "Action": [
                "sqs:ChangeMessageVisibility",
                "sqs:CreateQueue",
                "sqs:DeleteMessage",
                "sqs:DeleteQueue",
                "sqs:GetQueueAttributes",
                "sqs:GetQueueUrl",
                "sqs:ReceiveMessage",
                "sqs:SendMessage",
                "sqs:SetQueueAttributes"
            ],
            "Resource": [
                "arn:aws:sqs:{region}:*:{prefix}-*"
            ]
        }
    ]
}
```