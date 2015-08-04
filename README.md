Distance phrase reordering (DPR) model for MOSES

A. General description

The distance phrase reordering (DPR) model mainly focuses on the application of machine learning (ML) techniques to a specific problem in machine translation: learning the grammatical rules and content dependent changes, which are simplified as phrase reorderings. It models the mentioned problem with a classification framework and aims at improving the fluency of machine translation. Different from the lexicalized reordering model used in MOSES (Koehn, et al., 2005), this model considers the sentence context as well as the relationships between phrase movements, by means of a newly emerging structured learning paradigm. As observed by the authors, the DPR model works well on some language pairs that contain many differences in word ordering (e.g. Chinese-to-English). 

For more details, the readers are referred to the following paper: 

1) Ni Y, Saunders C, Szedmak S, Niranjan M. Exploitation of machine learning techniques in modeling phrase movements for machine translation. Journal of Machine Learning Research. 2011;12:1-30.

2) Ni Y, Saunders C, Szedmak S, Niranjan M. Handling phrase reorderings for machine translation. In Proceedings of the joint conference of the 47th Annual Meeting of the Association for Computational Linguistics and the 4th International Joint Conference on Natural Language Processing of the Asian Federation of Natural Language Processing. 2009.

B. Copyright
 
*************************************************************
Copyright (c) 2010, Yizhao Ni.
All rights reserved.

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

    * Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.
    * Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.
    * Redistributions of source code for commercial purposes should contact the copyright holder.
      
THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" 
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE 
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE 
ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE 
LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR 
CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF 
SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS 
INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN 
CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) 
ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE 
POSSIBILITY OF SUCH DAMAGE.


If you use this software package in your sientific work, please cite as

1) Ni Y, Saunders C, Szedmak S, Niranjan M. Exploitation of machine learning techniques in modeling phrase movements for machine translation. Journal of Machine Learning Research. 2011;12:1-30.

2) Ni Y, Saunders C, Szedmak S, Niranjan M. Handling phrase reorderings for machine translation. In Proceedings of the joint conference of the 47th Annual Meeting of the Association for Computational Linguistics and the 4th International Joint Conference on Natural Language Processing of the Asian Federation of Natural Language Processing. 2009.
*************************************************************

C. Source code

The model is integrated into MOSES as a feature function. Therefore, you also need the MOSES software package to run the program. The MOSES software package including the DPR model (DPR_MOSES.zip) is available at the following location:

https://github.com/skyloadGithub/DPR/DPR_MOSES.zip 

Alternatively, the source code is also available via Subversion from Sourceforge, by executing the following commands:

mkdir moses

svn co https://mosesdecoder.svn.sourceforge.net/svnroot/mosesdecoder/branches/DPR_MOSES moses

This will copy all source code (MOSES with DPR) to your local machine (located at the directory "/moses/").

To compile the MOSES model, the readers are reffered to the MOSES user guide: http://www.statmt.org/moses_steps.html

To compile the DPR model, you need to go to the directory "moses/DPR_model/" and execute the command 

./makeFile

If the program runs successfully, it will generate three executables "smt_mainProcess_configulation", "smt_mainProcess_construct_phraseDB" and "smt_mainProcess_generatePhraseOption".

D. How to use

The DPR package consits of two modules: a sample extraction module (smt_mainProcess_construct_phraseDB) and a DPR probability generation module (smt_mainProcess_generatePhraseOption). The extraction module is used to extract all samples (phrase pairs) for training a DPR model, while the geneartion module can then be used to generate the DPR probabilities for different phrase pairs.

1- Train a MOSES system

Since the DPR model requires some files from MOSES, you need to train a MOSES system before training a DPR model. The MOSES user guide (http://www.statmt.org/moses_steps.html) will help you to do this step. 

2- prerequisite


The DPR model requires the following files from a MOSES system:
	1) the source/target word-class dictionary. After training a MOSES system, the two files are located at "./corpus/fr.vcb.classes" (and en.vcb.classes). Alternatively, you can use "mkcls" to train more accurate word-class dictionaries (e.g. by increasing training rounds, using different number of word classes, etc).
	2) the word alignment file. The file is located at "./model/aligned.grow-diag-final-and".
	3) The phrase table generated by MOSES. The file is located at "./model/phrase-table.gz" and you need to unzip it before using it. Alternatively, to facilitate the processing time it is highly recommand to use a filted phrase table. That is, use the MOSES script "filter-model-given-input.pl" (see http://www.statmt.org/moses_steps.html) to filter the phrase table and use the filtered table in the filtered directory.

3. Generating a parameter configulation file

To obtain a DPR model, you need to generate a parameter configulation file by calling:

./smt_mainProcess_configulation myConfigulationFile

A file named "myConfigulationFile" will then be generated, which contains all the information needed for the rest of the process. You need to fill in all items listed below (certain items have been assigned default values):

	General part:
	1) SourceCorpusFile - the source corpus for the training (each line is a sentence).
	2) TargetCorpusFile - the target corpus for the training (each line is a sentence).
	3) SourceWordClassFile - the source word-class dictionary from MOSES or mkcls (fr.vcb.classes).
	4) TargetWordClassFile - the target word-class dictionary from MOSES or mkcls (en.vcb.classes).
	
	For extracting samples (phrase pairs) for the DPR model:
	5) alignmentFile - the word alignment file generated by MOSES.
	6) (output) phraseTableFile - the file containing the samples (phrase pairs) for the DPR model.
	7) TestFileName - only the source phrases appeared in this file will be extacted from the training corpus. In order to facilitate the training process, it is highly recommended to define this file as the combination of the develop and the test sets (i.e. a text that contains all source sentences from the develop and the test sets).

	For generating the DPR probabilities:
	8) PhraseTranslationTable - the phrase table generated by MOSES (unzip first). It is highly recommended to use the filtered phrase table.
	9) maxTranslations - the maximum number of translations for a source phrase (default 100).
	10) tableFilterLabel - 0: if the MOSES phrase table is un-filtered; 1 (default): if the MOSES phrase table is filtered.
	11) (output) weightMatrixFile - the filename of the DPR model.
	12) weightMatrixTrainLabel - 0: if you don't need to train the weight matrix (e.g. you have trained it before); 1 (default): train the weight matrix.
	13) (output) phraseOptionFile - The filename of phrase options. It stores the phrase options (phrase pairs) with their DPR probabilities for each sentence in the "TestFile". Line "i" contains the phrase options for sentence "i". This file will then be used in an MOSES decoder.
	14) TestFile - the file containing the source test sentences. The DPR probabilities will be generated for these sentences only.
	15) batchOutputLabel - 0: collect phrase options for one sentence at a time (use less memory but very slow); 1: collect phrase options for all sentences at a time (default and recommended, use large memory but very fast).

	For the DPR model:
	16) maxPhraseLength - the phrase pairs up to length "maxPhraseLength" (default 7) will be extracted.
	17) classSetup - the class setup of the DPR model, currently, the model only support 3-class setup and 5-class setup. See (Ni, et al., 2009) for details.
	18) distCut - prune the phrase pairs whose reordering distance is larger than "distCut" (default 15). To avoid some alignment errors caused by GIZA++.
	19) maxNgramSize - the maximum length of ngram used in the ngram dictionary (usually choose 3 or 4, default 4).
	20) windowSize - the window size around the source phrases (usually choose 3 or 4, default 3). See (Ni, et al., 2009) for details.
	21) minPrune - prune the ngram features that occur less than "minPrune" times (default 1). See (Ni, et al., 2009) for details.
	22) minTrainingExample - prune the source phrases that occur less than "minTrainingExample" times (default 10), because the discriminatvie model doesn't work well when the training size is too small.
	23) maxRound - the maximum number of iterations (default 500). See (Ni, et al., 2009) for details.
	24) setp - the step size (learning rate) of the DPR model (default 0.05). See (Ni, et al., 2009) for details.
	25) eTol - the error tolerance for training the DPR model (default 0.001). See (Ni, et al., 2009) for details.

3- Generating the training samples for the DPR model

After filling the configulation file. Generating the training samples for the DPR model is rather easy, just execute the command

./smt_mainProcess_construct_phraseDB myConfigulationFile

It will generate the following files for training the DPR model:
	1) SourceCorpusFile.tags - the word-class tags for the source corpus (each line is a sentence).
	2) TargetCorpusFile.tags - the word-class tags for the target corpus (each line is a sentence).
	3) SourceCorpusFile.ngramDict - the ngram feature dictionary constructed using the source word corpus.
	4) TargetCorpusFile.ngramDict - the ngram feature dictionary constructed using the target word corpus.
	5) SourceCorpusFile.tagsDict - the ngram word-class dictionary constructed using the source word-class corpus.
	6) TargetCorpusFile.tagsDict - the ngram word-class dictionary constructed using the target word-class corpus.
	7) phraseTableFile - the file containing the extracted samples (phrase pairs) for the DPR model.
	8) phraseTableFile.featureRelabel - the relabels of the ngram features.

4- Training the DPR model and generating the DPR probabilities

The final step is to execute the command

./smt_mainProcess_generatePhraseOption myConfigulationFile

The following files will then be generated:
	1) weightMatrixFile - the DPR model.
	2) weightMatrixFile.startPosition - the start position of each DPR sub-model (one for a source phrase).
	3) phraseOptionFile - the phrase options for the "TestFile" corpus (each line is a sentence).
	4) phraseOptionFile.startPosition - the start position of each line in "phraseOptionFile".

5- Integrating the DPR model into MOSES

To integrate the DPR model into MOSES, you need to use the MOSES software package provided in the above link (as the author has modified some MOSES source code).  Meanwhile, the following lines should be added in "model/moses.ini"

*************************************************************
[DPR-file]
/your_directory_to_phraseOptionFile/phraseOptionFile

[wDPR]
the weight for the DPR model (e.g. 0.5)

[class-DPR]
the class for the DPR model (choose 3 or 5 depending on the DPR model trained)
*************************************************************

This will tell the MOSES decoder where the DPR-probability file is located and what is the weight for the DPR model.

6- Minimal error-rating training (MERT)

To use MERT, you need to use the MOSES scripts package provided in the above link (as the author has modified some source code). The execution is the same:

./your_directory_to_scripts/training/mert-moses.pl ./your_directory_to_source/your_source_file ./your_directory_to_target/your_target_file ./your_directory_to_moses/moses-cmd/src/moses ./your_directory_to_model/model/moses.ini --working-dir ./your_working_directory/ --rootdir ./your_directory_to_scripts/ --decoder-flags "-v 0"

If you would like to switch on/off the DPR model or other reordering models, you can use the configulations "lambdas" and "activate". For example do the following:

./your_directory_to_scripts/training/mert-moses.pl ./your_directory_to_source/your_source_file ./your_directory_to_target/your_target_file ./your_directory_to_moses/moses-cmd/src/moses ./your_directory_to_model/model/moses.ini --working-dir ./your_working_directory/ --rootdir ./your_directory_to_scripts/ --decoder-flags "-v 0" --lambdas="wDPR:0.5,0.1-1.5" --activate=d_1,lm,tm,w,wDPR

The command tells MERT that the initial weight for the DPR model is 0.5 (you can also define weights for other parameters such as "d", "lm", "tm" and "w") and the range of the weight is between 0.1 and 1.5. Meanwhile, there are 5 weights needed tuning: d_1 (i.e. the wor distance-based reordering model), lm (the language model), tm (the phrase translation model), w (the word penalty) and wDPR (the DPR model). 

7- Decoding

When you obtain the tuned parameters for the MOSES decoder, use the following command to decode the test sentences:

./your_directory_to_moses/moses-cmd/src/moses -config /your_directory_to_model/model/moses.ini -input-file /your_directory_to_source/your_source_test 1> /your_directory_to_target/your_target_translation 2> /your_directory_to_log/log_file


Now, enjoy the new phrase reordering model!

E. Trouble shooting

When you compile the files or execute the commands and it reports errors, it might due to the different coding of Windows and Linux for CR (carriage return). You can try the Perl file "delDots.pl" to solve the problem. Try:

delDots.pl your_program your_new_program

F. Contact

If you have any more problem or suggestion, please contact: Yizhao.Ni@googlemail.com
