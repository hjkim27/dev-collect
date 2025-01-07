---
title: div.load() sample
date: 2023
type: javascript sample
---
# `div.laod()` sample example
## view 페이지 div 안에 출력

- [load 문서](https://api.jquery.com/load/)
- [callback 참고](https://www.w3schools.com/jquery/jquery_ajax_load.asp)

현재 load 후 페이지가 이동되는 문제가 있음.
추가 확인 필요
```javascript
function importPage(url){
    $('#hjkim').load(url, function(responseText, statusTxt, xhr){
        alert(statusTxt);
        console.log(xhr.status +":"+ JSON.stringify(xhr));
    });
}
```

## example 

- div.load(url) 함수 작업 
    ```javascript
    var uu;

    // url 로 호출된 페이지를 div#hjkim2 에 load
    async function importPage(url){
        $('#buttonDiv').css('display', 'none');
        $('div').css('display', 'none');
        $('#jejeong').css('display', 'block');

        $('#hjkim2').load(url, function(responseText, statusTxt, xhr){
            console.log("#hjkim2 load : "+xhr.status);
            console.log(JSON.stringify(xhr));
            
            start = new start2();
        });
    }

    // 함수 재정의
    function start2(){
        alert("start2");
        uu = path+'/verification/'+vid2;
        alert(uu)
        $('#jejeong').load(uu, function(responseText, statusTxt, xhr){
            console.log("#jejeong load : "+xhr.status);
        })
        return false;
    }
    ```
- javascript 함수 재정의
    - 페이지를 호출, 이동하는 부분을 수정하기 위해 사용

- 함수 및 변수를 호출하지 못하는 문제 확인
    - 함수 재정의
    - 변수 >> javascript 호출문제인 줄 알았으나, 단순 변수 선언부 문제로 확인   
      변수를 지역>전역으로 수정 후 정상 호출로 확인함.   
      카메라 사이즈가 전체로 보이지 않는 문제가 있음. 추가 확인 진행 필요

