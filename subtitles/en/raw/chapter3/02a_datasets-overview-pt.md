The Hugging Face Datasets library: A Quick overview. The Hugging Face Datasets library is a library that provides an API to quickly download many public datasets and preprocess them. In this video we will explore how to do that. The downloading part is easy: with the load_dataset function, you can directly download and cache a dataset from its identifier on the Dataset hub. Here we fetch the MRPC dataset from the GLUE benchmark, which is a dataset containing pairs of sentences where the task is to determine the paraphrases. The object returned by the load_dataset function is a DatasetDict, which is a sort of dictionary containing each split of our dataset. We can access each split by indexing with its name. This split is then an instance of the Dataset class, with columns (here sentence1, sentence2. label and idx) and rows. We can access a given element by its index. The amazing thing about the Hugging Face Datasets library is that everything is saved to disk using Apache Arrow, which means that even if your dataset is huge you won't get out of RAM: only the elements you request are loaded in memory. Accessing a slice of your dataset is as easy as one element. The result is then a dictionary with list of values for each keys (here the list of labels, the list of first sentences and the list of second sentences). The features attribute of a Dataset gives us more information about its columns. In particular, we can see here it gives us the correspondence between the integers and names for the labels. 0 stands for not equivalent and 1 for equivalent. To preprocess all the elements of our dataset, we need to tokenize them. Have a look at the video "Preprocess sentence pairs" for a refresher, but you just have to send the two sentences to the tokenizer with some additional keyword arguments. Here we indicate a maximum length of 128 and pad inputs shorter than this length, truncate inputs that are longer. We put all of this in a tokenize_function that we can directly apply to all the splits in our dataset with the map method. As long as the function returns a dictionary-like object, the map method will add new columns as needed or update existing ones. To speed up preprocessing and take advantage of the fact our tokenizer is backed by Rust thanks to the Hugging Face Tokenizers library, we can process several elements at the same time to our tokenize function, using the batched=True argument. Since the tokenizer can handle list of first/second sentences, the tokenize_function does not need to change for this. You can also use multiprocessing with the map method, check out its documentation! Once this is done, we are almost ready for training: we just remove the columns we don't need anymore with the remove_columns method, rename label to labels (since the models from Hugging Face Transformers expect that) and set the output format to our desired backend: torch, tensorflow or numpy. If needed, we can also generate a short sample of a dataset using the select method.