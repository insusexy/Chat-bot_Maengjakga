# Internship Project Wrap-Up 

### 1. 한글 신문 요약을 위한 실험

##### (1) 파일 구성

- prepare_data_ko.tar.gz

  > - bin/script: 한겨레, 경향 신문 데이터를 crawling 및 필터링하기 위한 쉘 스크립트
  > - bin/moran_2013/, bin/moran_cn/: 모란 형태소 분석기
  > - bin/api: 토크나이저 api

- pointer-generator-kor: 학습, 평가, 데모 실행

- hani-gh: 한겨레, 경향 신문 데이터 

- pyrouge: Summary 점수 평가 모듈

- apache-tomcat: HTTP 서버



##### (2) 데이터 구축

- java8 이상, mecab_ko 설치가 필요합니다(바이너리위치: /usr/local/bin/mecab)

- 도커에 이미 만들어 놓은 파일을 실행하신다면, *(2)데이터 구축*은 *Skip*하셔도 좋습니다.

  ​

######i) 네이버 뉴스를 수집합니다.

> `$ cd bin/script`
>
> `$ ./1.crawl.naver.sh`

###### ii) 네이버 뉴스에 광고나 기사와 관련 없는 내용이 붙어있어 "기사원문"을 타고 직접 수집합니다.

> `$ ./2.crawl.gh.sh`
>
> `$ ./3.crawl.hani.sh`

######iii) 정답 서머리의 길이가 35자 이하, 원문의 길이가 100자 이하 및 특수 문자를 필터링 합니다.

> `$ ./4.filtering_news.sh`

###### iv) 형태소 분석을 적용하여 신문기사 내용을 토크나이징 합니다.

> `$ ./5.mecab_tokenizer.sh`
>
> `$ ./6.moran_tokenizer.sh`

###### v) 토큰으로 나뉜 파일들을 필터링 합니다.

> `$ ./7.make.filter.stories.sh`
>
> `$ ./8.run.filter.stories.sh`

######vi) 필터링 된 데이터를 train, test, val로 나눕니다. 

> `$ ./9.split.data.sh`



##### ./data/moran/과 ./data/mecab 각각 mm_stories, nmm_stories폴더가 생기고 그 폴더에 train, val, test폴더가 생성되어 데이터가 나뉘어 들어가진 것을 확인하실 수 있습니다.



###### vii) binary files 생성

- 본 설명은 *moran의 medium-morpheme 데이터* 기준으로 만들었습니다.
- moran의 naive하게 적용한 일반 데이터 (nmm, no-medium-morpheme)이나 mecab-ko의 일반 데이터, medium-morpheme데이터를 만들 때 이름만 변경하셔서 동일하게 진행하시면 됩니다.

- 바이너리 파일이 있는 폴더 이름은 hani-gh입니다. 위 데이터를 생성한 폴더와 다른 폴더에서 진행해주세요.
- 실제 학습 및 평가 시 사용하고, 로그가 기록되는 폴더입니다.
- stories파일들을 bin파일로 만들어주는 파일이 make_datafiles.py입니다.

> - moran-mm을 위한 binary 폴더 생성
>
>   `$ cd hani-gh`
>
>   `$ mkdir finished_mm_moran`



> - make_datafiles.py 경로 수정
>
> - 위에서 만든 폴더 이름으로 수정해줍니다.
>
>   `25 finished_files_dir = "finished_mm_moran"`
>
> - 파일의 기본경로가 있다면 수정해주세요. 
>
> - /home/airi/부분을 수정해주시면 됩니다.
>
>   `159 fileList = os.listdir("/home/airi/" + stories_dir)`
>
>   `163 article, abstract = get_art_abs("/home/airi/" + stories_dir +files)`



> - make_datafiles.py 실행
>
> - " python make_datafiles.py <데이터파일 경로>"
>
>   ` python make_datafiles.py data/moran/mm_finished`
>
> - 약 1분 정도 소요됩니다.



*학습 준비*가 완료되었습니다!



##### (3) 학습, 평가하기

- 10.100.1.110 서버에서 작업시 GPU 디바이스 번호가 꼬여 있으므로 주의하세요.

- device 번호: 0 -> 3, 2 -> 1

  > 1) 학습하기
  >
  > - pointer-generator-kor/shell_script/train 폴더에서 쉘스크립트를 실행합니다.
  >
  > - 필요시 스크립트에서 경로를 수정해줍니다.
  >
  >   `$ ./run_train_moran_mm.sh`
  >
  >   ​
  >
  > - 실행 시 ascii code error가 난다면...
  >
  >   `$ export LANG=ko_KR.utf8`

  ​

  > 2) 평가하기
  >
  > - pyrouge 설치 합니다.
  >
  >   `$ cd pyrouge`
  >
  >   `$ sudo python setup.py install`
  >
  > - pyrouge가 설치된 경로로 홈 경로를 수정합니다.
  >
  >   `$ vi ~/.pyrouge/settings.ini`
  >
  > - pyrouge/pyrouge 폴더로 가서 test.py를 실행합니다.
  >
  >   `$ cd pyrouge`
  >
  >   `$ python test.py`
  >
  >   OK 라고 뜨면 정상적으로 설치가 완료된 것입니다.
  >
  >   ​
  >
  > - pointer-generator-kor/shell_script/decode 폴더에서 쉘스크립트를 실행합니다.
  >
  > - 필요시 스크립트에서 경로를 수정해줍니다.
  >
  >   `$ ./run_decode_moran_mm.sh`




##### (4) 데모

- 로컬(회사 내 본인 컴퓨터, 10.100.0.157)과 서버(10.100.1.110)와 통신하는 작업을 기준으로 작성이 되어있습니다. 
- 필요 시 해당 파일에서 주소를 수정하셔서 사용하시면 됩니다.

> 1) 로컬 준비
>
> - apache-tomcat/bin 에서 쉘스크립트를 실행합니다.  백그라운드로 실행합니다.
>
>   `$ ./startup.sh > &`
>
> - 토크나이징 api를 실행합니다.
>
>   `$ cd ~/bin/api/source/`
>
> - cab-ko, moran경로를 수정합니다.
>
>   `$ vi src/main/resources/application.properties `
>
> - 소스를  빌드합니다.
>
>   `$ ./mvnw install`
>
> - 서버를 기동시킵니다.
>
>   `$ cd ./target/`
>
>   `$ java -jar summarization-api-0.0.1-SNAPSHOT.jar`



> 2) 서버 준비
>
> - pointer-generator-kor/shell_script/shell4demo 폴더에서 쉘스크립트를 실행합니다. 
>
> - 백그라운드로 실행합니다.
>
>   `$ ./run_userinput_moran_mm.sh &`
>
>   ``$ ./run_userinput_moran_nmm.sh &``
>
>   `$ ./run_userinput_mecab_mm.sh &`
>
>   `$ ./run_userinput_mecab_nmm.sh &`

> 3) 데모화면 확인
>
> - web browser에서 자신의 로컬 ip address를 입력하시면 화면이 보이게 됩니다.
> - 한글신문은 han.html입니다.
> - ex) 10.100.0.157:8080/han.html



> 3) 데모 화면 수정
>
> - `$ cd apache-tomcat/webapps/ROOT/`
> - `$ vi han.html` 에서 수정하시면 됩니다.



### 2. 영어 신문 요약을 위한 실험

##### (1) 파일 구성

- pointer-generator-eng: 학습, 평가, 데모 실행 
- cnn-dailymail: cnn과 dailymail 신문 데이터
- apache-tomcat: HTTP 서버
- google: 번역 api
- stanford-corenlp-full-2016-10-31: tokenizer
- pyrouge: Summary 점수 평가




##### (2) 준비

- Github의 링크로 가서 데이터 구성과, 학습 진행 및 평가를 진행하시면 됩니다. 

- Github에는 파이썬 2, 3버전이 모두 업로드 되어 있습니다. 

  > [Github: pointer-generator(version 2)](https://github.com/abisee/pointer-generator "Github:  pointer-generator(version 2)")

  > [Github: pointer-generator(version 3)](https://github.com/becxer/pointer-generator/ "Github:  pointer-generator(version 3)")


- 유의할 점은 stanfordNLPtokenizer를 설치하실 때 *3.7.0 버전*을 설치하셔야 합니다.

  > https://stanfordnlp.github.io/CoreNLP/history.html

  ​

##### (3) 학습, 평가하기

- 학습 시, GPU 메모리가 8.6GB이상 필요합니다. 

- 10.100.1.110 서버에서 작업 시 GPU 디바이스 번호가 꼬여 있으므로 주의하세요.
  - device 번호: 0 -> 3, 2 -> 1

> 1) docker에서 작업하기
>
> - 110 서버로 로그인 합니다.
>
>   ```
>   $ ssh user01@10.100.1.110
>   $ password: airi!23$
>   ```
>
> - 쉘스크립트를 실행하거나, 다음 명령어를 사용하여 docker에 들어갑니다.
>
> - 두 개의 명령어 중 하나만 실행하시면 됩니다.
>   - 쉘 스크립트 실행
>
>     ```
>     $ cd kaggle_dsb2017
>     $ sh summarization.sh
>     ```
>
>   - 루트 권한으로 실행하기 (dd21389e6fb7는 container ID입니다.)
>
>     `$ docker exec -it dd21389e6fb7 /bin/bash`
>



>
> 2) 학습하기
>
> - pointer-generator-eng/shell_script/ 폴더에서 쉘스크립트를 실행합니다.
>
> - 필요시 스크립트에서 경로를 수정해줍니다.
>
>   `$ ./run_train.sh`



> 3) 평가하기
>
> - pyrouge 설치는 한글 신문과 동일합니다. 필요 시 설치해 주세요.
>
>
> - pointer-generator-eng/shell_script/ 폴더에서 쉘스크립트를 실행합니다.
>
> - 필요시 스크립트에서 경로를 수정해줍니다.
>
>   `$ ./run_decode.sh`



##### (4) 데모

- 로컬(회사 내 본인 컴퓨터, 10.100.0.157)과 서버(10.100.1.110)와 통신하는 기준으로 작업이 되어 있습니다. 
- 필요 시 해당파일에 주소를 수정하셔서 사용하시면 됩니다.

> 1) 로컬 준비
>
> - apache-tomcat/bin 에서 쉘스크립트를 실행합니다. 백그라운드로 실행합니다.
>
>   `$ ./startup.sh > &`



> 2) 서버 준비
>
> - pointer-generator-eng/shell_script/ 폴더에서 쉘스크립트를 실행합니다. 백그라운드로 실행합니다.
>
>   `$ ./run_userinput.sh &`



> 3) 데모 화면 수정
>
> `$ cd apache-tomcat/webapps/ROOT/`
>
> `$ vi index.html` 에서 수정하시면 됩니다.



web browser에서 자신의 로컬 ip address를 입력하시면 화면이 보이게 됩니다.

영어 신문은 index.html입니다.

> ex) 10.100.0.157:8080/index.html
