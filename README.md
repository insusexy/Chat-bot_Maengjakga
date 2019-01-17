
# 대화형 콘텐츠 아카이브
대화형 콘텐츠 데모를 위해서는 콘텐츠를 보여주기 위해서는 다음과 같은 프로그램 및 콘텐츠가 필요하다. 
 - Mediaiwki 서버
 - Wikitext 형식으로 작성한 콘텐츠
 - 한글 chatscript+ 엔진
 - chatscript top 포맷으로 작성된 스크립트 파일  
# MediaWiki 설치 
MediaWiki 동작을 위해서는 PHP 7.0.0+ 과 MySQL/MariaDB를 먼저 설치해야 한다. MediaWiki의 설치방법은 다음 사이트를 참조한다. 
[MediaWiki 설치 한글 가이드 문서](https://www.mediawiki.org/wiki/Manual:Running_MediaWiki_on_Debian_or_Ubuntu/ko)
[MediaWiki Download](https://www.mediawiki.org/wiki/Download)
Mediawiki 를 시작하기 위해서는 데이터베이스와 사용자를 지정하여야 한다. 추후 관리를 위해 데이터베이스 이름과 패스워드를 다음과 같이 정한다.  
 
 - 데이터베이스/패스워드 :  wikidb/airi
 - 편집 권한 사용자/패스워드 :	wiki/airi	
## MediaWiki header 정보 숨기기
mediawiki:Common.css 페이지를 검색한후 해당 정보 수정/추가
 - li#ca-talk { display: none; }  
 - li#ca-history { display: none; }  
 - li#ca-watch { display: none; }  
 - li#ca-move { display: none; }  
 - li#ca-delete { display: none; }  
 - li#ca-view { display: none; }
## MediaWiki footer 정보 숨기기
다음 페이지에 내용을 입력하거나 보이지 않게 하려면 ‘-’ 표시
 - MediaWiki:Privacy
 - MediaWiki:Aboutsite
 - MediaWiki:Disclaimers

저작권 표시 icon 의 경우에는 Mediawiki 디렉톨의 localsettings.php 내용을 수정해야함

## MediaWiki Left Panel  숨기기
MediaWiki:Vector.css 페이지에 다음 내용 추가

    /** Hide sidebar **/
    div#mw-panel {
      display: none;
    }
    #left-navigation {
      left: 1em;
    }
    #mw-head-base,
    div#content,
    div#footer {
      margin-left: 1em;
    }


# 데모 실행 절차
ChatBook/BINARIES 디렉토리에서 다음 명령을 실행하여 Chatscript 서버 실행

   
웹 브라우저에서 다음 주소 입력하여 서비스 실행 



 

# 아카이브 소스 파일 위치
한글Chatscript+는 $HOME/Chatscript 에 설치된 것을 가정한다.
 
|디렉토리명|파일 위치 |
|--|--|
|DON  |$HOME/Chatscript/RAWDATA/DON    |
|QUIBBLE |$HOME/Chatscript/RAWDATA/QUIBBLE  |
|ChatBook|/var/www/html/ChatBook |
 |

