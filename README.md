## Tweet Sentiment Models

This project contains various deep learning models to classify the sentiment of tweets as well as generate tweets for a given sentiment. The dataset used is the [cleaned-emotion-extraction-dataset-from-twitter](https://www.kaggle.com/datasets/kosweet/cleaned-emotion-extraction-dataset-from-twitter). This project was built using Python 3.10.10. No versions have been specified in requirements.txt.

To run experiements, parameters must be specified in hparams.yaml. Parameters used for the best performing variant of each model are already specified.

Two important parameters are related to tokenization. The first is "use_existing_tokenizer" which allows for loading of a tokenizer that has already been built. The second is "use_existing_tokens" which allows for reloading of previously tokenized data. These options are important for the CustomBPETokenizer as both building this tokenizer and encoding the text using it are extremely computationally expensive. Note that no internal checks are performed to ensure that any re-loaded tokenisations conform to new model/tokenisation parameters specified. For example, if the number of samples used is changed, particularly if its made smaller, "use_existing_tokenizer" and "use_existing_tokens" must be False to ensure the tokenizer is re-built and no "peeking" at the test set has occurred during building of the tokenizer. Saved tokenizations of the data are tagged with train/test/val and whether or not emojis have been removed. Once a CustomBPETokenizer has been built with a set number of samples, "use_existing_tokenizer" and "use_existing_tokens" can be set to True to re-use the existing tokenizer and tokens. The same tokenizer and/or already tokenized data can be re-used across all classifier models (provided number of samples and emojis is is the same). Where necessary, padding is performed AFTER tokenisation on a model specific basis and so no padding is completed on saved tokens.

Tokenization must be carried out on the first model training. For further training, I would strongly recommend always passing "use_existing_tokenzer : True" for any models using the CustomBPETokenizer to avoid re-building and overwriting any existing CustomBPETokenizer. Be warned that tokenizing the data is very time consuming. There is definitely optimisation that can be done here. 500,000 samples has been specified by default. If tokenisation parameters are changed, existing test tokens under /saved_tokenizers should be manually deleted before "use_existing_tokens" is set to True. This ensures thats the test tokens computed with old tokenizers are not being re-used during eval.

The dataset used is available at https://www.kaggle.com/datasets/kosweet/cleaned-emotion-extraction-dataset-from-twitter. It should be provided as "dataset(clean).csv" in the data directory. On the first run "--write_data True" should be passed as a command line argument. This specifies that the dataset needs to be split into test/val/train. If this has already been done, there is no need to pass --write_data True again unless the number of samples being used has changed.

Training is carried out by running "train.py <model_name>". The state dict of the best model as ranked by validation loss is saved.

Once a model is trained it can be evaluated using "eval.py <model_name>". This loads the best model state dict and evaluates it on the test set. This code also relies on the parameters specified in hparams.yaml. These params should not be changed between training and testing a model.

Also provided is the notebook "hyperparam_search.ipynb" that demonstrates the hyperparameter search completed using the Optuna package.

Once a decoder model has been built, tweets can be generated from it using the notebook "gen_tweets.py".

Examples of training the MLP and the Encoder can be found in README.ipynb. Similar commands can be used to run experiments for any of the other models. The tokenization had already been completed for the models below at the time this was ran. If re-running, tokens will be recomputed when the first training is started. Although no model weights are provided, figures and logs of some training runs are under /logs and /figures.