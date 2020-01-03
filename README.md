# s3-unzipper-go

## Description

unzip uploaded zip file to another S3 bucket via AWS Lambda in Go

![sequence](https://user-images.githubusercontent.com/24784855/71702582-99ee2980-2e13-11ea-997c-52d8780751c6.png)

## Production

### prerequisites

You have to prepare credentials with proper policies.

And,

- install [aws-cli](https://github.com/aws/aws-cli)
- install [aws-sam-cli](https://github.com/awslabs/aws-sam-cli). Docker is also required. Follow the instruction [here](https://github.com/awslabs/aws-sam-cli#installation).
- install [direnv](https://github.com/direnv/direnv)
- install [saw](https://github.com/TylerBrock/saw)
  - you can watch CloudWatch logs on your terminal
- set environment variables to [.envrc.sample](./.envrc.sample) and remove _.sample_.
  - _ZIPPED_ARTIFACT_BUCKET_ is S3 bucket name. It's S3① in the sequence and should be unique globally.
  - _UNZIPPED_ARTIFACT_BUCKET_ is S3 bucket name. It's S3② in the sequence and should be unique globally.
  - _STACK_BUCKET_ is S3 bucket name for artifacts of SAM and should be unique globally.

### deploy

```
$ dep ensure                       # to resolve dependency
$ aws s3 mb "s3://${STACK_BUCKET}" # for artifacts of SAM
$ make deploy
```

### behavior

Deploy is required before checking behavior.

```
$ saw groups
/aws/lambda/stack-unzipper-lambda-Unzipper-XXXXXXXXXXXX

$ saw watch /aws/lambda/stack-unzipper-lambda-Unzipper-XXXXXXXXXXXX

# open another window
$ aws s3 cp testdata/sample.zip "s3://${ZIPPED_ARTIFACT_BUCKET}"
```

## Local

You can test a behavior on test (`main_test.go`).

In the test, `setup` prepares 2 real S3 buckets because SAM local doesn't support local emulation of an S3.

One is for an even source that triggers an AWS Lambda and another is for a destination of unzipped artifacts.

Because S3 buckets created at the test are deleted on every test execution, idempotency is guaranteed.

## Articles (Japanese)

- [Go で学ぶ AWS Lambda（PDF、ePub セット版）](https://toshi0607.booth.pm/items/1034858)
  - This architecture is explained in detail in this book.
- [技術書典 5 で『Go で学ぶ AWS Lambda』を出展します #技術書典](http://toshi0607.com/programming/learning-aws-lambda-with-go/)
- [技術書典 5 の『Go で学ぶ AWS Lambda』の振り返りとフィードバックのお願い #技術書典](http://toshi0607.com/event/review-of-tbf5/)
