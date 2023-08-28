# dane-asr-worker

DANE worker for (Dutch) automatic speech recogition (ASR). For ASR we depend on [Kaldi_NL](https://github.com/opensource-spraakherkenning-nl/Kaldi_NL)

# Installation

## Run locally:

Prerequisites:

- Python 3.10.x
- [Poetry](https://python-poetry.org/)

```sh
poetry install
poetry shell
python ./worker.py
```

## Docker

The base image for this DANE worker is made available in our image registry. The source for the base image (base on Kaldi_NL) can be found [here](https://github.com/opensource-spraakherkenning-nl/Kaldi_NL)

```sh
docker build -t dane-asr-worker .
```

# Configuration

Make sure the create, and fill in to match your environment, the following `config.yml`:

```yaml
RABBITMQ:
    HOST: your-rabbit-mq-host  # set to your rabbitMQ server
    PORT: 5672
    EXCHANGE: DANE-exchange
    RESPONSE_QUEUE: DANE-response-queue
    USER: guest # change this for production mode
    PASSWORD: guest # change this for production mode
ELASTICSEARCH:
    HOST: ["elasticsearch-host"]  # set to your elasticsearch host
    PORT: 9200
    USER: "" # change this for production mode
    PASSWORD: "" # change this for production mode
    SCHEME: http  # OR https
    INDEX: your-dane-index  # change to your liking
LOCAL_KALDI:
    ASR_PACKAGE_NAME: asr-features.tar.gz  # default, no need to change
    ASR_WORD_JSON_FILE: words.json  # default, no need to change
    KALDI_NL_DIR: /opt/Kaldi_NL  # default location in Docker container
    KALDI_NL_DECODER: decode_OH.sh  # OR 'decode.sh'
    KALDI_NL_MODEL_DIR: /models  # ensure this folder is available in Docker container
    KALDI_NL_MODEL_FETCHER: entrypoint.sh  # default
FILE_SYSTEM:
    BASE_MOUNT: mount #'mount' when running locally
    INPUT_DIR: input-files
    OUTPUT_DIR: output-files/asr-output
PATHS:
    TEMP_FOLDER: mount/input-files
    OUT_FOLDER: mount/output-files
INPUT:
    DELETE_ON_COMPLETION: True
OUTPUT:
    DELETE_ON_COMPLETION: True
    TRANSFER_ON_COMPLETION: True  # S3 options below only if True
    S3_ENDPOINT_URL: http://localhost:9000  # S3 host
    S3_BUCKET: test-bucket  # bucket to put the files in
    S3_FOLDER_IN_BUCKET: assets  # folder within the bucket
DANE_DEPENDENCIES: ['DOWNLOAD'] # this param is optional, will be defaulted to [] if absent
```


# Run

The best way to run is within a Kubernetes environment, together with the following:

- DANE-server
- dane-download-worker
- RabbitMQ server
- Elasticsearch cluster

Documentation on how to setup this environment will be linked later on
