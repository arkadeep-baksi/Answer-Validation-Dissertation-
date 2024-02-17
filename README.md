# Answer-Validation-Dissertation

The topic of the Project/Dissertation is Contextual Answer Validation. Basically we are looking to a scenario where we are provided with a Context, a Question, the Correct answer to the Question and a Candidate Answer. We need to determine whether the candidate answer is a correct answer or not.

Diving deep into the problem we can divide it into Two Categories : 1) Questions having short answer 2) Questions having long answer
Primarily the approach to solve the different categories is different with the first one being a bit easier.

### Short Answer-Validation

First of all we quantitatively determine a short answer as some continuos stretch of words with atleast 10 words in it.
Now if we look carefully then it turns out that the Short Anwer-Validation problem is nothing but checking if the Correct Answer and the Candidate Answer are paraphrases or not. So it becomes clear that we need to build some kind of Paraphrase detector model to solve our problem.

#### Dataset

To build the model for Short Answer-Validation we extensively use and augment the SQuAD 2.0 Dataset. However SQuAD 2.0 contains lot of extremely short answers commonly termed as Factoid Answers.  So we sampled out only those which fit our criteria for short answers. The resulting sampled out SQuAD 2.0 dataset contained 4693 rows.

Apart from the SQuAD 2.0 several other datasets like MRPC, PAWS were also used.

#### 1) Base Model Building

To build the base model we used the SOTA BERT for Sequence Classification. Initially to train the BERT for Seq Clsf we used the famous MRPC(Microsoft Research Corpus)  Paraphrase Dataset. It contained ~5.6K paraphrases with corresponding labels. We trained the BERT for Seq CLsf on this dataset for 4 epochs.

Accuracy of the Model : 0.82

With this we are done with our base line model

#### 2) Fine-Tuning base line model on Augmented SQuAD 2.0

Now we have a base line model to validate short answers. However the MRPC dataset merely contains pair of sentences and a corresponding label whether they are paraphrases or not. So there is no concept of contextuality in the dataset. However as we are intending to solve the problem of contextual answer validation we need to fine-tune the base model on some other contextual data.

Here SQuAD 2.0 comes to our rescue. However SQuAD 2.0 contains only an answer not any paraphrase to it. But to train any model we needed to generate some positive examples( paraphrases) and some negative examples( distractors) for the correct answers of SQuAD 2.0 . 

###### Generating Positive (Paraphrases) Samples
We used the Backtranslation technique to generate Positive samples. Back Translation is a simple technique where we first convert the answer into some other language(
French we used) and then again back translate it to English. This way we generated Positive samples for the entire sampled out SQuAD 2.0

###### Generating Distractors
We used a BERT model to construct distractors for the answers in the sampled out SQuAD 2.0

After generating the necessary positive and negative samples we fine-tuned the model on this Augmented SQuAD 2.0 for 4 epochs

Accuracy of the Base-Model on Test data before fine-tuning: 0.83

Accuracy of the Base-Model on Test data after fine-tuning: 0.86

#### 3) Further Fine-Tuning the Model on PAWS and PAWS-Augmented SQuAD

After we have fine-tuned the model on Augmented SQuAD 2.0 we thought that the sapmles we have trained the model so far does'nt have much lexical overlap. We were curious how would the model perform incase the candidate answer have high lexical overlap with the correct answer but is actually incorrect. To test this we figured out some popular dataset w.r.t to this area of NLP called PAWS( Paraphrase Adversaries for Word Scrambling). We figured out that the publically available PAWS dataset is a good point to start. However again we need some contextuality and hence needed to generate PAWS like paraphrases for SQuAD 2.0

###### Generating PAWS like Paraphrases
We used the PAWS dataset and trained a T5 transformer model to generate PAWS like paraphrases for SQuAD 2.0

After this appending the PAWS dataset with PAWS-Augmented SQuAD 2.0 we fine tuned our model again for 3 epochs.

Accuracy of the Model on Test data before fine-tuning on PAWS : 0.50

Accuracy of the Model on Test data after fine-tuning on PAWS : 0.80
