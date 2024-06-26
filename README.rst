Thai Language Toolkit Project  version 1.2.1
============================================

TLTK is a Python package for Thai language processing: syllable, word, discouse unit segmentation, pos tagging, named entity recognition, grapheme2phoneme, ipa transcription, romanization, etc.  TLTK requires Python 3.4 or higher.
The project is a part of open source software developed at Chulalongkorn University.

Input : must be utf8 Thai texts.

Updates:
--------

More compound words are added in the dictionary. Version 1.1.3-1.1.5 have many entries that are not a word and contain a few errors. They are removed in the latest version.

NER tagger model was updated because dictionary used in word segmetation was updated. 


tltk.nlp  :  basic tools for Thai language processing.
------------------------------------------------------

>>tltk.nlp.chunk(Text) : chunk parsing. The output includes markups for word segments (|), elementary discourse units (<u/>), pos tags , and named entities, e.g. tltk.nlp.chunk("สำนักงานเขตจตุจักรชี้แจงว่า ได้นำป้ายประกาศเตือนปลิงไปปักตามแหล่งน้ำ ในเขตอำเภอเมือง จังหวัดอ่างทอง หลังจากนายสุกิจ อายุ 65 ปี ถูกปลิงกัดแล้วไม่ได้ไปพบแพทย์")

=> '<NEo>สำนักงาน/NOUN|เขต/NOUN|จตุจักร/PROPN|</NEo>ชี้แจง/VERB|ว่า/SCONJ|<s/>/SYM|ได้/AUX|นำ/VERB|ป้าย/NOUN|ประกาศ/VERB|เตือน/VERB|ปลิง/NOUN|ไป/VERB|ปัก/VERB|ตาม/ADP|แหล่งน้ำ/NOUN|<u/>ใน/ADP|<NEl>เขต/NOUN|อำเภอ/NOUN|เมือง/NOUN|<s/>/SYM|จังหวัด/NOUN|อ่างทอง/PROPN|</NEl><u/>หลังจาก/SCONJ|<NEp>นาย/NOUN|สุ/PROPN|กิจ/NOUN|<s/>/SYM|อายุ/NOUN|</NEp><u/>65/NUM|<s/>/SYM|ปี/NOUN|<u/>ถูก/AUX|ปลิง/VERB|กัด/VERB|แล้ว/ADV|ไม่ได้/AUX|ไป/VERB|พบ/VERB|แพทย์/NOUN|<u/>'

>>tltk.nlp.ner([(w,pos),....]) : module for named entity recognition (person, organization, location), e.g. tltk.nlp.ner([('สำนักงาน', 'NOUN'), ('เขต', 'NOUN'), ('จตุจักร', 'PROPN'), ('ชี้แจง', 'VERB'), ('ว่า', 'SCONJ'), ('<s/>', 'PUNCT')])

=> [('สำนักงาน', 'NOUN', 'B-O'), ('เขต', 'NOUN', 'I-O'), ('จตุจักร', 'PROPN', 'I-O'), ('ชี้แจง', 'VERB', 'O'), ('ว่า', 'SCONJ', 'O'), ('<s/>', 'PUNCT', 'O')]
Named entity recognition is based on crf model adapted from http://sklearn-crfsuite.readthedocs.io/en/latest/tutorial.html Model is trainned with a corpus of 200,000 words containing 7,354 named entities. B-O, I-O are tags for organizations; B-P, I-P are tags for persons; and B-L, I-L are tags for locations.

>>tltk.nlp.pos_tag(Text,WordSegmentOption) : word segmentation and POS tagging (using nltk.tag.perceptron), e.g. tltk.nlp.pos_tag('โปรแกรมสำหรับใส่แท็กหมวดคำภาษาไทย วันนี้ใช้งานได้บ้างแล้ว') or  

=> [[('โปรแกรม', 'NOUN'), ('สำหรับ', 'ADP'), ('ใส่', 'VERB'), ('แท็ก', 'NOUN'), ('หมวดคำ', 'NOUN'), ('ภาษาไทย', 'PROPN'), ('<s/>', 'PUNCT')], [('วันนี้', 'NOUN'), ('ใช้งาน', 'VERB'), ('ได้', 'ADV'), ('บ้าง', 'ADV'), ('แล้ว', 'ADV'), ('<s/>', 'PUNCT')]] 
By default word_segment(Text,"colloc") will be used, but if option = "mm", word_segment(Text,"mm") will be used; POS tag set is based on Universal POS tags.. http://universaldependencies.org/u/pos/index.html
nltk.tag.perceptron model is used for POS tagging. It is trainned with POS-tagged subcorpus in TNC (148,000 words)


>>tltk.nlp.pos_tag_wordlist(WordLst) : Same as "tltk.nlp.pos_tag", but the input is a word list, [w1,w2,...]

>>tltk.nlp.segment(Text) : segment a paragraph into elementary discourse units (edu) marked with <u/> and segment words in each edu e.g. tltk.nlp.segment("แต่อาจเพราะนกกินปลีอกเหลืองเป็นพ่อแม่มือใหม่ รังที่ทำจึงไม่ค่อยแข็งแรง วันหนึ่งรังก็ฉีกเกือบขาดเป็นสองท่อนห้อยต่องแต่ง ผมพยายามหาอุปกรณ์มายึดรังกลับคืนรูปทรงเดิม ขณะที่แม่นกกินปลีอกเหลืองส่งเสียงโวยวายอยู่ใกล้ ๆ แต่สุดท้ายไม่สำเร็จ สองสามวันต่อมารังที่ช่วยซ่อมก็พังไป ไม่เห็นแม่นกบินกลับมาอีกเลย") 

=> 'แต่|อาจ|เพราะ|นกกินปลีอกเหลือง|เป็น|พ่อแม่|มือใหม่|<s/>|รัง|ที่|ทำ|จึง|ไม่|ค่อย|แข็งแรง<u/>วัน|หนึ่ง|รัง|ก็|ฉีก|เกือบ|ขาด|เป็น|สอง|ท่อน|ห้อย|ต่องแต่ง<u/>ผม|พยายาม|หา|อุปกรณ์|มา|ยึด|รัง|กลับคืน|รูปทรง|เดิม<u/>ขณะ|ที่|แม่|นกกินปลีอกเหลือง|ส่งเสียง|โวยวาย|อยู่|ใกล้|ๆ<u/>แต่|สุดท้าย|ไม่|สำเร็จ|<s/>|สอง|สาม|วัน|ต่อ|มา|รัง|ที่|ช่วย|ซ่อม|ก็|พัง|ไป<u/>ไม่|เห็น|แม่|นก|บิน|กลับ|มา|อีก|เลย<u/>'   edu segmentation is based on syllable input using RandomForestClassifier model, which is trained on an edu-segmented corpus (approx. 7,000 edus)  created and used in Nalinee’s thesis 

>>tltk.nlp.word_segment(Text,method='mm|ngram|colloc') : word segmentation using either maximum matching or ngram or maximum collocation approach. 'colloc' is used by default. Please note that the first run of ngram method would take a long time because TNC.3g will be loaded for ngram calculation. e.g. 

tltk.nlp.word_segment('ผู้สื่อข่าวรายงานว่านายกรัฐมนตรีไม่มาทำงานที่ทำเนียบรัฐบาล')
=> 'ผู้สื่อข่าว|รายงาน|ว่า|นายกรัฐมนตรี|ไม่|มา|ทำงาน|ที่|ทำเนียบรัฐบาล|<s/>'

>>tltk.nlp.syl_segment(Text) : syllable segmentation using 3gram statistics e.g. tltk.nlp.syl_segment('โปรแกรมสำหรับประมวลผลภาษาไทย') 

=> 'โปร~แกรม~สำ~หรับ~ประ~มวล~ผล~ภา~ษา~ไทย<s/>'

>>tltk.nlp.word_segment_nbest(Text, N) : return the best N segmentations based on the assumption of minimum word approach. e.g. tltk.nlp.word_segment_nbest('คนขับรถประจำทางปรับอากาศ"',10) 

=> [['คนขับ|รถประจำทาง|ปรับอากาศ', 'คนขับรถ|ประจำทาง|ปรับอากาศ', 'คน|ขับ|รถประจำทาง|ปรับอากาศ', 'คน|ขับรถ|ประจำทาง|ปรับอากาศ', 'คนขับ|รถ|ประจำทาง|ปรับอากาศ', 'คนขับรถ|ประจำ|ทาง|ปรับอากาศ', 'คนขับ|รถประจำทาง|ปรับ|อากาศ', 'คนขับรถ|ประจำทาง|ปรับ|อากาศ', 'คน|ขับ|รถ|ประจำทาง|ปรับอากาศ', 'คนขับ|ร|ถ|ประจำทาง|ปรับอากาศ']]

>>tltk.nlp.g2p(Text)  : return Word segments and pronunciations
e.g. tltk.nlp.g2p("สถาบันอุดมศึกษาไม่สามารถก้าวให้ทันการเปลี่ยนแปลงของตลาดแรงงาน")  

=> "สถา~บัน~อุ~ดม~ศึก~ษา|ไม่|สา~มารถ|ก้าว|ให้|ทัน|การ|เปลี่ยน~แปลง|ของ|ตลาด~แรง~งาน<tr/>sa1'thaa4~ban0~?u1~dom0~sUk1~saa4|maj2|saa4~maat2|kaaw2|haj2|than0|kaan0|pliian1~plxxN0|khOON4|ta1'laat1~rxxN0~Naan0|<s/>"

>>tltk.nlp.th2ipa(Text) : return Thai transcription in IPA forms
e.g. tltk.nlp.th2ipa("ลงแม่น้ำรอเดินไปหาปลา") 

=> 'loŋ1 mɛː3.naːm4 rᴐː1 dɤːn1 paj1 haː5 plaː1 <s/>'

>>tltk.nlp.th2roman(Text) : return Thai romanization according to Royal Thai Institute guideline.
.e.g. tltk.nlp.th2roman("คือเขาเดินเลยลงไปรอในแม่น้ำสะอาดไปหามะปราง") 

=> 'khue khaw doen loei long pai ro nai maenam sa-at pai ha maprang <s/>'

>>tltk.nlp.g2p_all(Text) : return all transcriptions (IPA) as a list of tuple (syllable_list, transcription). Transcription is based on syllable reading rules. It could be different from th2ipa.
e.g. tltk.nlp.g2p_all("รอยกร่าง") 

=> [('รอย~กร่าง', 'rᴐːj1.ka2.raːŋ2'), ('รอย~กร่าง', 'rᴐːj1.kraːŋ2'), ('รอ~ยก~ร่าง', 'rᴐː1.jok4.raːŋ3')]

>>tltk.nlp.spell_candidates(Word) : list of possible correct words using minimum edit distance, e.g. tltk.nlp.spell_candidates('รักษ')

=> ['รัก', 'ทักษ', 'รักษา', 'รักษ์']


Other defined functions in the package:
>>tltk.nlp.reset_thaidict() : clear dictionary content
>>tltk.nlp.read_thaidict(DictFile) : add a new dictionary  e.g. tltk.nlp.read_thaidict('BEST.dict')
>>tltk.nlp.check_thaidict(Word) : check whether Word exists in the dictionary

tltk.corpus  :   basic tools for corpus enquiry
-----------------------------------------------

>>tltk.corpus.TNC_load()  by default load TNC.3g. The file can be in the working directory or TLTK package directory

>>tltk.corpus.trigram_load(TRIGRAM)  ###  load Trigram data from other sourse saved in tab delimited format "W1\tW2\tW3\tFreq"  e.g.  tltk.corpus.load3gram('TNC.3g') 'TNC.3g' can be downloaded separately from Thai National Corpus Project.

>>tltk.corpus.unigram(w1)   return normalized frequecy (frequency/million) of w1 from the corpus

>>tltk.corpus.bigram(w1,w2)   return frequency/million of Bigram w1-w2 from the corpus e.g. tltk.corpus.bigram("หาย","ดี") => 2.331959592765809

>>tltk.corpus.trigram(w1,w2,w3)  return frequency/million of Trigram w1-w2-w3 from the corpus

>>tltk.corpus.collocates(w, stat="chi2", direct="both", span=2, limit=10, minfq=1)   ### return all collocates of w, STAT = {freq,mi,chi2} DIR={left,right,both}  SPAN={1,2}  The output is a list of tuples  ((w1,w2), stat). e.g. tltk.corpus.collocates("วิ่ง",limit=5) 

=> [(('วิ่ง', 'แจ้น'), 86633.93952758134), (('วิ่ง', 'ตื๋อ'), 77175.29122642518), (('วิ่ง', 'กระหืดกระหอบ'), 48598.79465339733), (('วิ่ง', 'ปรู๊ด'), 41111.63720974819), (('ลู่', 'วิ่ง'), 33990.56839021914)]

>>tltk.corpus.w2v_load()  by deafult load word2vec file "TNCc5model.bin". The file can be in the working directory or TLTK package directory

>>tltk.corpus.w2v_exist(w) check whether w has a vector representation  e.g. tltk.corpus.w2v_exist("อาหาร") => True

>>tltk.corpus.w2v(w)  return vector representation of w

>>tltk.corpus.similarity(w1,w2) e.g. tltk.corpus.similarity("อาหาร","อาหารว่าง") => 0.783551877546

>>tltk.corpus.similar_words(w, n=10, cutoff=0., score="n")  e.g. tltk.corpus.similar_words("อาหาร",n=5, score="y") 

=> [('อาหารว่าง', 0.7835519313812256), ('ของว่าง', 0.7366500496864319), ('ของหวาน', 0.703102707862854), ('เนื้อสัตว์', 0.6960341930389404), ('ผลไม้', 0.6641997694969177)]

>>tltk.corpus.outofgroup([w1,w2,w3,...]) e.g. tltk.corpus.outofgroup(["น้ำ","อาหาร","ข้าว","รถยนต์","ผัก"]) => "รถยนต์"

>>tltk.corpus.analogy(w1,w2,w3,n=1) e.g. tltk.corpus.analogy('ผู้ชาย','พ่อ','แม่') => ['ผู้หญิง']  ผู้ชาย - พ่อ + แม่ =  ผู้หญิง

>>tltk.corpus.w2v_plot([w1,w2,w3,...])  => plot a scratter graph of w1-wn in two dimensions


Notes
-----

- Word segmentation is based on a maximum collocation approach described in this publication: "Aroonmanakun, W. 2002. Collocation and Thai Word Segmentation. In Thanaruk Theeramunkong and Virach Sornlertlamvanich, eds. Proceedings of the Fifth Symposium on Natural Language Processing & The Fifth Oriental COCOSDA Workshop. Pathumthani: Sirindhorn International Institute of Technology. 68-75." (http://pioneer.chula.ac.th/~awirote/ling/SNLP2002-0051c.pdf)

- Use tltk.nlp.word_segment(Text) or tltk.nlp.syl_segment(Text) for segmenting Thai texts. Syllable segmentation now is based on a trigram model trainned on 3.1 million syllable corpus. Input text is a paragraph of Thai texts which can be mixed with English texts. Spaces in the paragraph will be marked as "<s/>". Word boundary is marked by "|". Syllable boundary is marked by "~". Syllables here are written syllables. One written syllable may be pronounced as two syllables, i.e. "สกัด" is segemnted here as one written syllable, but it is pronounced as two syllables "sa1-kat1".

- Determining words in a sentence is based on the dictionary and maximum collocation strength between syllables. Since many compounds and idioms, e.g. 'เตาไมโครเวฟ', 'ไฟฟ้ากระแสสลับ', 'ปีงบประมาณ', 'อุโมงค์ใต้ดิน', 'อาหารจานด่วน', 'ปูนขาวผสมพิเศษ', 'เต้นแร้งเต้นกา' etc., are included in the standard dictionary, these will likely be segmented as one word. For applications that prefer shortest meaningful words (i.e. 'รถ|โดยสาร', 'คน|ใช้', 'กลาง|คืน', 'ต้น|ไม้' as segmented in BEST corpus), users should reset the default dictionary used in this package and reload a new dictionary containing only simple words or shortest meaningful words. Use "reset_thaidict()" to clear default dictionary content, and "read_thaidict('DICT_FIILE')" to load a new dictionary. A list of words compiled from BEST corpus is included in this package as a file 'BEST.dict' 

- The standard dictionary used in this package has more then 65,000 entries including abbreviations and transliterations compiled from various sources. A dictionary of 8,700 proper names e.g. country names, organization names, location names, animal names, plant names, food names, ..., such as 'อุซเบกิสถาน', 'สำนักเลขาธิการนายกรัฐมนตรี', 'วัดใหญ่สุวรรณาราม', 'หนอนเจาะลำต้นข้าวโพด', 'ปลาหมึกกระเทียมพริกไทย', are also added as a list of words in the system.

- For segmenting a specific domain text, a specialized dicionary can be used by adding more dictionary before segmenting texts. This can be done by calling read_thaidict("SPECIALIZED_DICT"). Please note that the dictionary is a text file in "iso-8859-11" encoding. The format is one word per one line.

- 'setence segment' or actually 'edu segment' is a process to break a paragraph into a chunk of discourse units, which usually are a clause. It is based on RandomForestClassifier model, which is trained on an edu-segmented corpus (approx. 7,000 edus) created and used in Nalinee's thesis (http://www.arts.chula.ac.th/~ling/thesis/2556MA-LING-Nalinee.pdf). Accuracy of the model is 97.8%. The reason behind using edu can be found in [Aroonmanakun, W. 2007. Thoughts on Word and Sentence Segmentation in Thai. In Proceedings of the Seventh Symposium on Natural Language Processing, Dec 13-15, 2007, Pattaya, Thailand. 85-90.] [Intasaw, N. and Aroonmanakun, W. 2013. Basic Principles for Segmenting Thai EDUs. in Proceedings of 27th Pacific Asia Conference on Language, Information, and Computation, pages 491-498, Nov 22-24, 2013, Taipei.]

- 'grapheme to phoneme' (g2p), as well as IPA transcription (th2ipa) and Thai romanization (th2roman) is based on the hybrid approach presented in the paper "A Unified Model of Thai Romanization and Word Segmentation". The Thai Royal Institute guidline for Thai romanization can be downloaded from "http://www.arts.chula.ac.th/~ling/tts/ThaiRoman.pdf", or "http://www.royin.go.th/?page_id=619" [Aroonmanakun, W., and W. Rivepiboon. 2004. A Unified Model of Thai Word Segmentation and Romanization. In  Proceedings of The 18th Pacific Asia Conference on Language, Information and Computation, Dec 8-10, 2004, Tokyo, Japan. 205-214.] (http://www.aclweb.org/anthology/Y04-1021)

Remarks
-------

- TNC Trigram data (TNC.3g)  and  TNC word2vec (TNCc5model.bin) can be downloaded from TNC website. http://www.arts.chula.ac.th/ling/tnc/searchtnc/
- Module "spell_candidates" is modified from Peter Norvig's Python codes at http://norvig.com/spell-correct.html 
- BEST corpus is the corpus released by NECTEC  (https://www.nectec.or.th/corpus/) 
- Universal POS tags are used in this project. For more information, please see http://universaldependencies.org/u/pos/index.html 
- pos_tag is based on PerceptronTagger in nltk.tag.perceptron. It is trained with TNC data manually pos-taged (approx. 148,000 words). Accuracy on pos tagging is 91.68%.  NLTK PerceptronTagger is a port of the Textblob Averaged Perceptron Tagger, which can be found at https://explosion.ai/blog/part-of-speech-pos-tagger-in-python 
- named entiy recognition module is a CRF model adapted from this tutorial (http://sklearn-crfsuite.readthedocs.io/en/latest/tutorial.html). The model is trained with NER data used in Sasimimon's and Nutcha's theses (altogether 7,354 names in a corpus of 183,300 words). (http://pioneer.chula.ac.th/~awirote/Data-Nutcha.zip, http://pioneer.chula.ac.th/~awirote/Data-Sasiwimon.zip )  Accuracy of the model is 92%.

