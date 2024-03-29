# DISS - Text Categorizer

Text Categorizer is a tool that implements a configurable pipeline of methods used to train models that predict the categories of textual data.

For training, it provides the ability to obtain information on the confidence of the trained models and perform cross-validation.

For prediction, it provides a server that answers queries with the classification predicted by the models.

## Getting Started

These instructions will get you a copy of the project up and running on your local machine.

There are two supported manners of using this tool.
The first one is to use it natively.
The other one is to use Docker.
The first one is the recommended one because it is more stable and the following problems can occur using Docker:
- Progress bars (that are generated using tqdm) are not shown.
- When using the prediction server, CTRL+C (used to stop the service) which generates SIGINT is translated to SIGTERM, not being detected by Flask. (Flask is simply killed.)
- The order of the output can be wrong because our output is sent using a logger, but the output of other packages is not.
- The debug mode on the prediction server doesn't work because the listener of file changes doesn't detect correctly the location of the files.

### Prerequisites

- To execute natively, a machine with Anaconda or Miniconda installed is required.
- To execute using Docker, only Docker is required and Docker Compose is recommended.

### Installing/Updating

Here are presented the instructions on how to install/update all the dependencies necessary to execute the tool in different environments.

* \<path-to-DISS\> is the path of folder "DISS".

To install/update natively, open a shell (an Anaconda prompt is recommended on Windows and Bash is recommended on Linux) and type the following commands:
```
cd <path-to-DISS>
conda env update -f text_categorizer/environment.yml
```

To install/update using Docker, open a shell and type the following commands:
```
cd <path-to-DISS>
docker-compose build
```

## Executing

Here are presented the instructions on how to execute the tool in different environments.

* \<path-to-DISS\> is the path of folder "DISS".

* \<configuration file\> is the path of the configuration file used. ("config.ini" is provided as example.)

* \<port\> is the port that will be used by the prediction server to listen for queries.

### Trainer

To execute natively on Windows, open a shell (an Anaconda prompt is recommended) and type the following commands:
```
cd <path-to-DISS>
conda activate text-categorizer
python text_categorizer <configuration file>
```

To execute natively on Linux, open a shell (Bash is recommended) and type the following commands:
```
cd <path-to-DISS>
source activate text-categorizer
python3 text_categorizer <configuration file>
```

To execute using Docker, open a shell and type the following commands:
```
cd <path-to-DISS>
docker-compose up -d text_categorizer-trainer
docker-compose logs -f # Shows the output. (Press CTRL+C to close.)
```
To stop the Docker container, type:
```
docker-compose down -t <seconds> text_categorizer-trainer # Please provide a large value for <seconds> so that there is enough time to save the documents that have not been preprocessed.
```

### Prediction Server

To execute natively on Windows, open a shell (an Anaconda prompt is recommended) and type the following commands:
```
cd <path-to-DISS>
conda activate text-categorizer
python text_categorizer/prediction_server.py <configuration file> <port>
```

To execute natively on Linux, open a shell (Bash is recommended) and type the following commands:
```
cd <path-to-DISS>
source activate text-categorizer
python3 text_categorizer/prediction_server.py <configuration file> <port>
```

To execute using Docker, open a shell and type the following commands:
```
cd <path-to-DISS>
docker-compose up -d text_categorizer-prediction_server
docker-compose logs -f # Shows the output. (Press CTRL+C to close.)
```
To stop the Docker container, type:
```
docker-compose down text_categorizer-prediction_server
```

### REST Request

To send a REST request to the prediction server, perform the following operations:
1. Train a classifier (generating a model).
2. Start the prediction server.
3. Open a REST client or prepare corresponding code.
4. Set the "Content-Type" header as "application/json".
5. Set the authentication credentials (the default is "admin" both as username and password).
6. Create a JSON body with a dictionary that contains two keys, "text" and "classifier", where the value for "text" is the text to give to the classifier and the value for "classifier" is the name of one of the trained classifiers. Examples of bodies:
    - {"text": "Example text...", "classifier": "RandomForestClassifier"}
    - {"text": "Example text...", "classifier": "SVC"}

## Built With

* [StanfordNLP](https://stanfordnlp.github.io/stanfordnlp/) - Used for preprocessing.
* [NLTK](http://www.nltk.org/) - Used for preprocessing.
* [scikit-learn](https://scikit-learn.org/stable/) - Used to extract features and train/use the predictive models.

## Authors

* **Luís Barbosa** - [LuisVilarBarbosa](https://github.com/LuisVilarBarbosa)

## Acknowledgments

* The layout of this README was partially inspired on https://gist.github.com/PurpleBooth/109311bb0361f32d87a2.

# Development Notes

- The code has been tested on Windows 10, MX Linux 18.2 (Debian stable based) and Docker images for Miniconda (Debian based).

- In some situations, the tool may present a reference to a document with index X where X is equal to the number of the row of the document in the Excel file.

- Pickle is used to dump and load data to and from files. This protocol is the fastest of the tested protocols, but is considered insecure. Please take this information into consideration.

- The documents ignored during preprocessing are also ignored when preparing feature extraction, counting for the number of documents ignored when preparing feature extraction. The number of ignored documents is only shown if greater than zero.

- Consulting the file "log.txt" is essential because it shows several more information than the console.

- The code is relatively generic and can be used as a basis for other experiments, but should be tuned for practical applications.

- The tool in this repository is no longer being maintained.
