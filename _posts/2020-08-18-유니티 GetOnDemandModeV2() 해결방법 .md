# 해결방법

유니티 혹은 에셋 파이프라인 V2 이전 파일의 Import 문제로 생기는 버그이며  
정확히는 메타파일의 GUID가 null인 파일이 있는경우 문제가 발생한다.  
https://github.com/shlifedev/MyScripts/blob/master/GetOnDemandModeV2Fixer.ps 에 있는 파워셸 스크립트를  
유니티 루트폴더에 넣고 파워셸로 실행시켜서 guid가 null인 파일이 찾아지면
해당 파일을 삭제하면 GetOnDemandModeV2() 오류는 사라진다


 