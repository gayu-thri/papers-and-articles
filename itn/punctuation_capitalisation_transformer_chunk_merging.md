<!-- Output copied to clipboard! -->

<!-- You have some errors, warnings, or alerts. If you are using reckless mode, turn it off to see inline alerts.
* ERRORs: 0
* WARNINGs: 0
* ALERTS: 5 -->

**<span style="text-decoration:underline;">Fast and Accurate Capitalization and Punctuation for Automatic Speech Recognition Using Transformer and Chunk Merging - Vietnam</span>** 

 

[https://arxiv.org/pdf/1908.02404.pdf](https://arxiv.org/pdf/1908.02404.pdf) 

 



* Problem: Standardizing the output of ASR such as capitalization and punctuation restoration for long-speech transcription. 
* Why is both punctuation & capitalization needed?  
    * Moreover, when ASR results are fed into NLP models to perform <span style="text-decoration:underline;">machine translation (MT) or name entity recognition (NER), punctuation and word capitalization are crucial pieces of information</span> that can help to boost the performance 
* Abstract:  
    * Other papers – First, model only handled one task. Second, long I/P sentences -> Split into fix-length and non-overlapped chunks before feeding into the model – prone to bad prediction of words around chunk's boundary as there's no enough both left and right context.  
* Proposal: Method to restore the punctuation and capitalization for long-speech ASR transcription. 
    * ASR output -> Overlapped-Chunk Split Module 
    * Split segments -> Punctuation Capitalization Module (processing chunks is paralleled) 
    * Output chunks -> Overlapped-Chunk Merging Module 
* The method is based on below in one go  
    * Transformer models  
        * Identical encoders and identical decoder stacked upon each other 
            * Each encoder – self-attention layer to extract surrounding words info 
            * Followed by Feed-Forward Network 
        * hidden layers=6, word embedding size=256, batch size=4096; epochs=200; number of heads in transformer model is 8. 
    * Over-lapped chunk merging that allows us to  
        * (1), build a single model that performs punctuation and capitalization in one go, and  
        * (2), perform decoding in parallel while improving the prediction accuracy.  
* Experiments on British National Corpus showed that the proposed approach outperforms existing methods in both accuracy and decoding speed. 
* **<span style="text-decoration:underline;">Algorithm for Overlapped-Chunk Split and Merging</span>** 
    * Hypothesis: There is not enough context information around the area, leading to the poor performance of the model 
    * Proposal: 
        * split long input sentences into chunks 
            * chunk size of k words 
            * **<span style="text-decoration:underline;">a sliding window of k/2 words so that 2 consecutive chunks are overlapped.</span>** 
              
![alt_text](../../images/chunk_merging.png "image_tooltip")

* The output of the model are merged – only keep predictions of the model with enough context information 
* Challenging part 
    * While splitting input sentences into overlapped chunks is straight-forward as we only need to decide the chunk and over-lapped size, 
        * **<span style="text-decoration:underline;">merging the overlapped results is more difficult</span>** 
    * The <span style="text-decoration:underline;">output of the overlapped region between 2 consecutive chunks can be different</span> 
        * Need to decide which words to keep & remove to form complete sentence 
    * **_min_words_cut_** 
        * indicates the <span style="text-decoration:underline;">number of words at the end the first chunk to be removed</span>  
        * and also the number of words to be kept at the end of overlapped words in the second chunk. 
        * ranges from **0 to the overlap size** 
        * value of 0 
            * the whole overlapped words in the first chunk are kept  
            * while the overlapped words in the second chunk are removed 
            * The same principle when **_min_words_cut_** equals to the **_overlapped size_**. 
 
![alt_text](../../images/min_words_cut.png "image_tooltip")

]
 
* Dataset Preparation 
    * Only alphabet characters and three punctuations (, . ?) are kept 
    * Punctuation belongs to previous word. Ex: laptop, mobile 
    * Split data into chunks according to split algorithm (Figure 4 above) 
* Corpus 
    * British National Corpus (BNC ) - 100 million words 
        * Covers British English from late 20<sup>th</sup> century 
          
![alt_text](../../images/bnc_corpus.png "image_tooltip")

* Evaluation 
    * Precision, Recall, F1 scores 
    * Converted output words and punctuation to the 6-class encoded format 
        * U, L, ., ,,?, $ 
    * Prediction of lowercase and blank space are good in all model – hence ignored in table 
      
![alt_text](../../images/chunk_merging_plain_text_scores.png "image_tooltip")

* The matrix shows comma is the most difficult class to predict 
    * Comma is often mis-predicted as blank character 
* Model with plain text outperforms ones with encoded text (U,.,?, etc)  
    * However,** model with encoded text – small model size & faster for inference** 
          
![alt_text](../../images/chunk_merging_encoded_text_scores.png "image_tooltip")
