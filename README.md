[수도권_지하철_통근_분석기_경로수정v2 (1).html](https://github.com/user-attachments/files/26834261/_._._._.v2.1.html)
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>역세권 개꿀 지역을 찾아서</title>
  <script src="https://unpkg.com/react@18/umd/react.production.min.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js" crossorigin></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <style>
    body {
      margin: 0;
      padding: 0;
      font-family: 'Pretendard', 'Noto Sans KR', -apple-system, sans-serif;
    }
  </style>
</head>
<body>
  <div id="root"></div>
  <script type="text/babel">
    const { useState, useMemo, useCallback, useEffect, useRef } = React;

    // ─── 노선 데이터 ─────────────────────────────────────────
    const LINES_DATA = {
      "1호선": { color:"#0052A4", stations:["소요산","동두천","보산","동두천중앙","지행","덕정","덕계","양주","녹양","가능","의정부","회룡","망월사","도봉산","도봉","방학","창동","녹천","월계","광운대","석계","신이문","외대앞","회기","청량리","제기동","신설동","동묘앞","동대문","종로5가","종로3가","종각","시청","서울역","남영","용산","노량진","대방","신길","영등포","신도림","구로","구일","개봉","오류동","온수","역곡","소사","부천","중동","송내","부개","부평","백운","동암","간석","주안","도화","제물포","도원","동인천","인천"], times:[3,2,3,2,3,2,3,3,2,3,2,2,2,2,2,2,3,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,3,3,2,2,3,3,3,2,2,2,2,3,2,2,2,2,2,2,2,2,2,2,2,2,2,2] },
      "1호선-경부": { color:"#0052A4", stations:["구로","가산디지털단지","독산","금천구청","석수","관악","안양","명학","금정","군포","당정","의왕","성균관대","화서","수원","세류","병점","세마","오산대","오산","진위","송탄","서정리","평택지제","평택","성환","직산","두정","천안","봉명","쌍용","아산","배방","온양온천","신창"], times:[3,2,2,3,2,2,2,2,3,2,2,3,2,2,3,3,3,3,2,3,3,2,3,3,3,4,3,3,3,2,3,4,2,3] },
      "2호선": { color:"#00A84D", stations:["시청","을지로입구","을지로3가","을지로4가","동대문역사문화공원","신당","상왕십리","왕십리","한양대","뚝섬","성수","건대입구","구의","강변","잠실나루","잠실","잠실새내","종합운동장","삼성","선릉","역삼","강남","교대","서초","방배","사당","낙성대","서울대입구","봉천","신림","신대방","구로디지털단지","대림","신도림","문래","영등포구청","당산","합정","홍대입구","신촌","이대","아현","충정로"], times:[2,2,2,2,2.3,2.5,2.3,2,2,2,2.2,2.5,2.8,2.5,2.8,2.2,2.2,2.3,2.3,2.2,2.2,2.3,2.2,2,2.2,2.3,2.2,2.2,2.2,2.3,2.5,2.5,2.8,2.2,2.3,2.3,2.3,2.2,2.2,2,2,2,2], circular:true },
      "2호선-성수지선": { color:"#00A84D", stations:["성수","용답","신답","용두","신설동"], times:[2.2,2.2,2.2,2.2] },
      "2호선-신정지선": { color:"#00A84D", stations:["신도림","도림천","양천구청","신정네거리","까치산"], times:[2.2,2.3,2.5,2.5] },
      "3호선": { color:"#EF7C1C", stations:["대화","주엽","정발산","마두","백석","대곡","화정","원당","삼송","지축","구파발","연신내","불광","녹번","홍제","무악재","독립문","경복궁","안국","종로3가","을지로3가","충무로","동대입구","약수","금호","옥수","압구정","신사","잠원","고속터미널","교대","남부터미널","양재","매봉","도곡","대치","학여울","대청","일원","수서","가락시장","경찰병원","오금"], times:[3,2,2,2,3,2,2,3,3,2,3,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2] },
      "4호선": { color:"#00A4E3", stations:["진접","오남","별내별가람","당고개","상계","노원","창동","쌍문","수유","미아","미아사거리","길음","성신여대입구","한성대입구","혜화","동대문","동대문역사문화공원","충무로","명동","회현","서울역","숙대입구","삼각지","신용산","이촌","동작","이수","사당","남태령","선바위","경마공원","대공원","과천","정부과천청사","인덕원","평촌","범계","금정","산본","수리산","대야미","반월","상록수","한대앞","중앙","고잔","초지","안산","신길온천","정왕","오이도"], times:[3,3,3,2,2,2,3,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,3,2,2,2,3,3,2,2,2,2,2,2,2,2,2,2,2,3,2,2,2,2,2,2,2,3,2] },
      "5호선": { color:"#996CAC", stations:["방화","개화산","김포공항","송정","마곡","발산","우장산","화곡","까치산","신정","목동","오목교","양평","영등포구청","영등포시장","신길","여의도","여의나루","마포","공덕","애오개","충정로","서대문","광화문","종로3가","을지로4가","동대문역사문화공원","청구","신금호","행당","왕십리","마장","답십리","장한평","군자","아차산","광나루","천호","강동","길동","굽은다리","명일","고덕","상일동","둔촌동","올림픽공원","방이","오금","개롱","거여","마천"], times:[2,2,3,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2] },
      "5호선-하남": { color:"#996CAC", stations:["강동","둔촌오륜","중앙보훈병원","고덕강일","미사","하남풍산","하남시청","하남검단산"], times:[2,3,3,2,2,2,2] },
      "6호선": { color:"#CD7C2F", stations:["응암","역촌","불광","독바위","연신내","구산","새절","증산","디지털미디어시티","월드컵경기장","마포구청","망원","합정","상수","광흥창","대흥","공덕","효창공원앞","삼각지","녹사평","이태원","한강진","버티고개","약수","청구","신당","동묘앞","창신","보문","안암","고려대","월곡","상월곡","돌곶이","석계","태릉입구","화랑대","봉화산","신내"], times:[2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2] },
      "7호선": { color:"#747F00", stations:["장암","도봉산","수락산","마들","노원","중계","하계","공릉","태릉입구","먹골","중화","상봉","면목","사가정","용마산","중곡","군자","어린이대공원","건대입구","뚝섬유원지","청담","강남구청","학동","논현","반포","고속터미널","내방","이수","남성","숭실대입구","상도","장승배기","신대방삼거리","보라매","신풍","대림","남구로","가산디지털단지","철산","광명사거리","천왕","온수","까치울","부천종합운동장","춘의","신중동","부천시청","상동","삼산체육관","굴포천","대장"], times:[3,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2] },
      "8호선": { color:"#E6186C", stations:["암사","천호","강동구청","몽촌토성","잠실","석촌","송파","가락시장","문정","장지","복정","산성","남한산성입구","단대오거리","신흥","수진","모란"], times:[2,2,2,2,2,2,2,2,2,2,3,2,2,2,2,2] },
      "9호선": { color:"#BDB092", stations:["개화","김포공항","공항시장","신방화","마곡나루","양천향교","가양","증미","등촌","염창","신목동","선유도","당산","국회의사당","여의도","샛강","노량진","노들","흑석","동작","구반포","신반포","고속터미널","사평","신논현","언주","선정릉","삼성중앙","봉은사","종합운동장","삼전","석촌고분","석촌","송파나루","한성백제","올림픽공원","둔촌오륜","중앙보훈병원"], times:[3,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,3,2] },
      "경의중앙선": { color:"#77C4A3", stations:["문산","파주","월롱","금촌","금릉","운정","야당","탄현","일산","풍산","백마","곡산","대곡","능곡","행신","강매","화전","수색","디지털미디어시티","가좌","신촌(경의)","서강대","홍대입구","공덕","서울역","용산","이촌","옥수","응봉","왕십리","청량리","회기","중랑","상봉","망우","양원","구리","도농","양정","덕소","도심","팔당","운길산","양수","신원","국수","아신","오빈","양평","원덕","용문","지평"], times:[4,3,3,2,3,2,2,3,2,2,2,3,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,3,2,2,2,2,3,2,2,3,3,3,4,3,2,3,3,3,3,3,3,3,4] },
      "수인분당선": { color:"#F5A200", stations:["청량리","왕십리","서울숲","압구정로데오","강남구청","선정릉","선릉","한티","도곡","구룡","개포동","대모산입구","수서","복정","가천대","태평","모란","야탑","이매","서현","수내","정자","미금","오리","죽전","보정","구성","신갈","기흥","상갈","청명","영통","망포","매탄권선","수원시청","매교","수원","고색","오목천","어천","야목","사리","한대앞","중앙","고잔","초지","안산","신길온천","정왕","오이도","달월","월곶","소래포구","인하대","숭의","신포","인천"], times:[3,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,3,2,2,2,3,2,2,2,2,2,2,2,2,2,3,3,3,3,2,2,2,2,2,2,2,3,2,3,2,2,2,2,2] },
      "신분당선": { color:"#D31145", stations:["신사","논현","신논현","강남","양재","양재시민의숲","청계산입구","판교","정자","미금","동천","수지구청","성복","상현","광교중앙","광교"], times:[2,2,2,2,3,2,3,2,2,3,2,2,2,3,2] },
      "공항철도": { color:"#0090D2", stations:["서울역","공덕","홍대입구","디지털미디어시티","김포공항","계양","검암","청라국제도시","영종","운서","공항화물청사","인천공항1터미널","인천공항2터미널"], times:[3,3,3,5,5,3,4,5,3,3,3,3] },
      "경춘선": { color:"#0C8E72", stations:["청량리","회기","중랑","상봉","망우","갈매","별내","퇴계원","사릉","금곡","평내호평","천마산","마석","대성리","청평","상천","가평","굴봉산","백양리","강촌","김유정","남춘천","춘천"], times:[2,2,2,2,3,3,2,3,3,2,3,2,4,3,3,3,3,2,4,2,2,3] },
      "우이신설선": { color:"#B7C452", stations:["북한산우이","솔밭공원","4·19민주묘지","가오리","화계","삼양","삼양사거리","솔샘","북한산보국문","정릉","성신여대입구","보문","신설동"], times:[2,2,2,2,2,2,2,2,2,2,2,2] },
      "신림선": { color:"#6789CA", stations:["관악산","서울대벤처타운","서원","당곡","난향","난곡","서울대입구(신림)","신림","보라매","보라매병원","보라매공원","대방","샛강"], times:[2,2,2,2,2,2,2,2,2,2,2,2] },
      "GTX-A": { color:"#9A6292", stations:["운정","킨텍스","대곡","연신내","서울역","삼성","수서","성남","용인","동탄"], times:[5,4,6,5,6,4,6,8,7] },
      "김포골드라인": { color:"#AD8605", stations:["양촌","구래","마산","장기","운양","걸포북변","사우(김포시청)","풍무","고촌","김포공항"], times:[2,2,2,2,2,2,2,2,3] },
    };

    const TRANSFERS = {
      "서울역":[["1호선","4호선",5],["1호선","경의중앙선",7],["1호선","공항철도",8],["4호선","경의중앙선",5],["4호선","공항철도",6],["경의중앙선","공항철도",5],["1호선","GTX-A",5],["4호선","GTX-A",5]],
      "시청":[["1호선","2호선",3]],"종로3가":[["1호선","3호선",4],["1호선","5호선",5],["3호선","5호선",4]],
      "동대문":[["1호선","4호선",3]],"동대문역사문화공원":[["2호선","4호선",4],["2호선","5호선",5],["4호선","5호선",4]],
      "신도림":[["1호선","2호선",3]],"을지로3가":[["2호선","3호선",3]],"을지로4가":[["2호선","5호선",3]],
      "충무로":[["3호선","4호선",3]],"왕십리":[["2호선","5호선",4],["2호선","경의중앙선",5],["2호선","수인분당선",5],["5호선","경의중앙선",4],["5호선","수인분당선",4],["경의중앙선","수인분당선",3]],
      "건대입구":[["2호선","7호선",3]],"잠실":[["2호선","8호선",3]],"종합운동장":[["2호선","9호선",3]],
      "교대":[["2호선","3호선",3]],"사당":[["2호선","4호선",3]],"성수":[["2호선","2호선-성수지선",0]],
      "신설동":[["1호선","2호선-성수지선",4],["1호선","우이신설선",4],["2호선-성수지선","우이신설선",3]],
      "까치산":[["2호선-신정지선","5호선",3]],"연신내":[["3호선","6호선",3],["3호선","GTX-A",4],["6호선","GTX-A",4]],
      "약수":[["3호선","6호선",3]],"고속터미널":[["3호선","7호선",4],["3호선","9호선",5],["7호선","9호선",4]],
      "옥수":[["3호선","경의중앙선",3]],"금정":[["1호선-경부","4호선",3]],"삼각지":[["4호선","6호선",3]],"용산":[["1호선","경의중앙선",2]],"이촌":[["4호선","경의중앙선",2]],
      "동작":[["4호선","9호선",3]],"이수":[["4호선","7호선",3]],"창동":[["1호선","4호선",3]],"노원":[["4호선","7호선",3]],
      "충정로":[["2호선","5호선",3]],"공덕":[["5호선","6호선",4],["5호선","경의중앙선",5],["5호선","공항철도",5],["6호선","경의중앙선",4],["6호선","공항철도",4],["경의중앙선","공항철도",3]],
      "청구":[["5호선","6호선",3]],"신당":[["2호선","6호선",3]],"여의도":[["5호선","9호선",4]],
      "합정":[["2호선","6호선",3]],"홍대입구":[["2호선","경의중앙선",4],["2호선","공항철도",5],["경의중앙선","공항철도",3]],
      "디지털미디어시티":[["6호선","경의중앙선",3],["6호선","공항철도",4],["경의중앙선","공항철도",3]],
      "석계":[["1호선","6호선",3]],"태릉입구":[["6호선","7호선",3]],"상봉":[["7호선","경의중앙선",4],["7호선","경춘선",4],["경의중앙선","경춘선",0]],
      "군자":[["5호선","7호선",3]],"대림":[["2호선","7호선",3]],"가산디지털단지":[["1호선-경부","7호선",3]],
      "온수":[["1호선","7호선",4]],"천호":[["5호선","8호선",3]],"가락시장":[["3호선","8호선",3]],
      "복정":[["8호선","수인분당선",3]],"모란":[["8호선","수인분당선",3]],
      "김포공항":[["5호선","9호선",5],["5호선","공항철도",6],["9호선","공항철도",4],["공항철도","김포골드라인",3]],
      "당산":[["2호선","9호선",3]],"노량진":[["1호선","9호선",4]],"삼성":[["2호선","9호선",4],["2호선","GTX-A",5],["9호선","GTX-A",4]],
      "선릉":[["2호선","수인분당선",3]],"강남":[["2호선","신분당선",3]],"강남구청":[["7호선","수인분당선",3]],
      "선정릉":[["9호선","수인분당선",3]],"정자":[["수인분당선","신분당선",3]],"미금":[["수인분당선","신분당선",3]],
      "양재":[["3호선","신분당선",3]],"도곡":[["3호선","수인분당선",3]],"수서":[["3호선","수인분당선",3],["3호선","GTX-A",4],["수인분당선","GTX-A",4]],
      "오금":[["3호선","5호선",3]],"영등포구청":[["2호선","5호선",3]],
      "청량리":[["1호선","경의중앙선",3],["1호선","경춘선",3],["1호선","수인분당선",4],["경의중앙선","경춘선",0],["경의중앙선","수인분당선",3],["경춘선","수인분당선",3]],
      "회기":[["1호선","경의중앙선",2],["1호선","경춘선",2]],
      "한대앞":[["4호선","수인분당선",3]],"초지":[["4호선","수인분당선",3],["4호선","서해선",3],["수인분당선","서해선",3]],
      "오이도":[["4호선","수인분당선",3]],"불광":[["3호선","6호선",3]],"보문":[["6호선","우이신설선",3]],
      "성신여대입구":[["4호선","우이신설선",3]],"동묘앞":[["1호선","6호선",3]],"도봉산":[["1호선","7호선",4]],
      "석촌":[["8호선","9호선",3]],"올림픽공원":[["5호선","9호선",3]],"강동":[["5호선","5호선-하남",0]],
      "샛강":[["9호선","신림선",3]],"대방":[["1호선","신림선",3]],"보라매":[["7호선","신림선",3]],
      "신림":[["2호선","신림선",4]],"서울대입구":[["2호선","신림선",5]],
      "신논현":[["9호선","신분당선",3]],"논현":[["7호선","신분당선",4]],"신사":[["3호선","신분당선",3]],
      "기흥":[["수인분당선","용인경전철",4]],"소사":[["1호선","서해선",3]],
    };

    // ─── 구별 역 그룹 (AI 검색 단위) ─────────────────────────
    const DISTRICT_GROUPS = [
      { name:"강남구", stations:["강남","역삼","선릉","삼성","압구정","청담","개포동","대치","도곡","학여울","대청","일원","수서","강남구청","압구정로데오","선정릉"] },
      { name:"서초구", stations:["교대","서초","방배","사당","잠원","고속터미널","신반포","구반포","남부터미널","양재","양재시민의숲","매봉","청계산입구"] },
      { name:"송파구", stations:["잠실","잠실새내","종합운동장","삼전","석촌","석촌고분","송파나루","한성백제","올림픽공원","방이","오금","송파","가락시장","문정","장지"] },
      { name:"용산구", stations:["용산","삼각지","신용산","이촌","한강진","이태원","녹사평","효창공원앞","남영"] },
      { name:"마포구", stations:["공덕","마포","합정","망원","홍대입구","애오개","대흥","광흥창","상수","마포구청","월드컵경기장"] },
      { name:"성동·광진구", stations:["왕십리","성수","행당","옥수","금호","약수","한양대","서울숲","건대입구","구의","강변","뚝섬유원지","어린이대공원"] },
      { name:"중구·종로구", stations:["시청","을지로입구","을지로3가","을지로4가","충무로","명동","회현","동대문","광화문","종각","종로3가","혜화","안국","경복궁"] },
      { name:"영등포·동작구", stations:["여의도","영등포구청","영등포","신길","대방","문래","선유도","노량진","동작","이수","흑석","상도","숭실대입구","보라매","장승배기"] },
      { name:"관악·구로·금천구", stations:["신림","서울대입구","낙성대","구로","신도림","구로디지털단지","가산디지털단지","남구로","금천구청","독산"] },
      { name:"양천·강서구", stations:["목동","오목교","양천구청","마곡나루","마곡","화곡","발산","우장산","까치산"] },
      { name:"강동·강북구", stations:["천호","강동구청","고덕","명일","둔촌동","암사","몽촌토성","길동","수유","미아사거리","미아"] },
      { name:"성북·노원·도봉구", stations:["길음","성신여대입구","한성대입구","보문","고려대","월곡","정릉","노원","중계","하계","상계","당고개","도봉","방학","쌍문","창동"] },
      { name:"은평·서대문구", stations:["연신내","불광","녹번","구산","홍제","이대","신촌","아현","서대문"] },
      { name:"경기 성남·과천", stations:["정자","미금","수내","서현","이매","야탑","판교","모란","태평","복정","과천","정부과천청사","대공원","경마공원"] },
      { name:"경기 안양·군포·의왕", stations:["평촌","범계","인덕원","금정","산본","군포","안양"] },
      { name:"경기 용인·수원", stations:["수지구청","광교중앙","광교","성복","상현","동천","기흥","죽전","보정","구성","신갈","오리","영통","청명","망포","수원시청","수원"] },
      { name:"경기 고양·파주", stations:["마두","정발산","주엽","대화","백석","삼송","지축","구파발","대곡"] },
      { name:"경기 하남·남양주·구리", stations:["미사","하남풍산","하남시청","별내","평내호평","구리"] },
    ];

    // ─── 하드코딩 기준가 (2026.04 KB시세·실거래 기반, 역세권 아파트 평균) ──
    const HOUSING_FALLBACK = {"압구정": 19784, "구룡": 14110, "개포동": 14110, "고속터미널": 13819, "반포": 13819, "구반포": 13819, "신반포": 13819, "사평": 13819, "잠원": 13522, "종합운동장": 13234, "잠실": 13234, "잠실나루": 13234, "잠실새내": 13234, "몽촌토성": 13234, "수서": 12254, "한티": 11645, "대치": 11645, "학여울": 11645, "대청": 11636, "일원": 11636, "대모산입구": 11636, "선정릉": 11355, "강남구청": 11355, "삼성": 11355, "삼성중앙": 11355, "봉은사": 11355, "뚝섬": 10598, "서울숲": 10598, "이촌": 10312, "서대문": 10258, "여의도": 10123, "여의나루": 10123, "국회의사당": 10123, "샛강": 10123, "서빙고": 10033, "압구정로데오": 9511, "청담": 9511, "대공원": 9443, "과천": 9443, "정부과천청사": 9443, "도곡": 9130, "매봉": 9130, "아현": 8949, "애오개": 8949, "용산": 8660, "신용산": 8660, "옥수": 8617, "흑석": 8270, "수내": 8120, "신금호": 8011, "공덕": 7997, "남영": 7975, "강남": 7715, "선릉": 7715, "역삼": 7715, "신논현": 7715, "성수": 7697, "금호": 7690, "광나루": 7601, "서현": 7467, "왕십리": 7416, "행당": 7416, "복정": 7413, "장지": 7413, "가락시장": 7403, "경찰병원": 7403, "신사": 7358, "목동": 7312, "오목교": 7312, "신목동": 7312, "삼각지": 7289, "학동": 7260, "논현": 7260, "언주": 7260, "효창공원앞": 7225, "상일동": 7214, "고덕": 7214, "판교": 7097, "응봉": 7090, "정자": 7083, "교대": 7082, "서초": 7082, "남부터미널": 7082, "송파": 7058, "상수": 7046, "서강대": 7028, "한남": 7019, "한강진": 7019, "방배": 7005, "남태령": 7005, "내방": 7005, "상왕십리": 6942, "올림픽공원": 6891, "방이": 6891, "명일": 6854, "뚝섬유원지": 6660, "숙대입구": 6611, "문정": 6523, "이매": 6348, "미금": 6314, "강변": 6240, "구의": 6240, "아차산": 6240, "동작": 6236, "오금": 6182, "개롱": 6182, "오리": 6154, "거여": 6129, "녹사평": 6109, "이태원": 6109, "영등포": 6081, "월드컵경기장": 6029, "마포구청": 6029, "서울": 5997, "서울(경의중앙선)": 5997, "석촌": 5997, "양천구청": 5957, "신정네거리": 5957, "신정": 5957, "암사": 5917, "사당": 5874, "총신대입구(이수)": 5874, "남성": 5874, "동대문역사문화공원": 5832, "약수": 5832, "신당": 5832, "청구": 5832, "동대입구": 5832, "버티고개": 5832, "증산": 5761, "마포": 5701, "상도": 5678, "숭실대입구": 5678, "장승배기": 5678, "신길": 5615, "대방": 5615, "신풍": 5615, "노량진": 5586, "노들": 5586, "충무로": 5574, "을지로입구": 5574, "명동": 5574, "회현": 5574, "광화문": 5574, "광흥창": 5532, "마곡": 5522, "마곡나루": 5522, "양재": 5493, "마장": 5477, "독립문": 5465, "경복궁": 5465, "영등포구청": 5450, "둔촌동": 5392, "양천향교": 5322, "가양": 5322, "증미": 5322, "가좌": 5321, "광명": 5245, "신흥": 5232, "문래": 5212, "야탑": 5191, "선유도": 5175, "신도림": 5159, "디지털미디어시티": 5136, "신답": 5119, "용두": 5119, "길음": 5057, "홍대입구": 5025, "양평": 4917, "수색": 4893, "을지로3가": 4846, "종각": 4846, "신대방삼거리": 4846, "보라매": 4846, "광교": 4774, "용답": 4748, "발산": 4674, "우장산": 4674, "녹번": 4624, "답십리": 4615, "합정": 4518, "굽은다리": 4509, "강동구청": 4509, "동대문": 4466, "신설동": 4466, "이대": 4448, "망원": 4374, "염창": 4324, "천호": 4321, "강동": 4321, "홍제": 4295, "무악재": 4295, "응암": 4290, "낙성대": 4200, "서울대입구": 4200, "봉천": 4200, "등촌": 4197, "김포공항": 4137, "송정": 4137, "공항시장": 4137, "군자": 4131, "어린이대공원": 4131, "시청": 4118, "안국": 4118, "마천": 4095, "길동": 4073, "철산": 4052, "불광": 4009, "독바위": 4009, "광명사거리": 3984, "광교중앙": 3946, "돌곶이": 3928, "수지구청": 3904, "중곡": 3899, "까치산": 3898, "화곡": 3898, "제기동": 3890, "대림": 3847, "한성대입구": 3846, "혜화": 3800, "인덕원": 3790, "외대앞": 3786, "신이문": 3786, "보문": 3766, "성신여대입구": 3760, "당산": 3754, "창신": 3753, "광운대": 3751, "석계": 3751, "월계": 3751, "매교": 3745, "동천": 3727, "평촌": 3701, "범계": 3701, "월곡": 3678, "면목": 3673, "사가정": 3673, "용마산": 3673, "독산": 3637, "지축": 3622, "장한평": 3610, "고려대": 3595, "구파발": 3590, "건대입구": 3540, "한양대": 3537, "하계": 3535, "청량리": 3513, "중계": 3484, "회기": 3482, "방화": 3472, "개화산": 3472, "개화": 3472, "신방화": 3472, "충정로": 3465, "정발산": 3460, "구리": 3444, "산성": 3442, "남한산성입구": 3442, "단대오거리": 3442, "중화": 3438, "영등포시장": 3415, "구일": 3359, "구로디지털단지": 3359, "도림천": 3359, "구로": 3359, "남구로": 3359, "노원": 3355, "당고개": 3355, "상계": 3355, "수락산": 3355, "마들": 3355, "동묘앞": 3348, "종로5가": 3348, "천왕": 3328, "창동": 3326, "녹천": 3326, "도농": 3322, "미아": 3280, "미아사거리": 3280, "삼양": 3280, "삼양사거리": 3280, "솔샘": 3280, "태릉입구": 3269, "공릉": 3269, "화랑대": 3269, "가천대": 3216, "태평": 3216, "개봉": 3214, "온수": 3179, "역곡": 3179, "상봉": 3169, "중랑": 3169, "기흥": 3166, "강남대": 3166, "신림": 3160, "신대방": 3160, "먹골": 3154, "연신내": 3141, "관악": 3133, "안양": 3133, "명학": 3133, "상월곡": 3128, "수진": 3125, "상현": 3115, "지제": 3080, "성복": 3076, "망포": 3070, "북한산보국문": 3055, "정릉": 3055, "망우": 3039, "양원": 3039, "센트럴파크": 3030, "국제업무지구": 3030, "캠퍼스타운": 3030, "테크노파크": 3030, "지식정보단지": 3030, "인천대입구": 3030, "갈매": 3028, "죽전": 2992, "경기광주": 2924, "원흥": 2902, "신내": 2888, "봉화산": 2888, "가산디지털단지": 2867, "신촌(경의중앙선)": 2858, "대흥": 2858, "보정": 2856, "석수": 2804, "역촌": 2786, "419민주묘지": 2756, "가오리": 2756, "화계": 2756, "수유": 2756, "솔밭공원": 2756, "신촌": 2754, "안산": 2700, "별내": 2678, "신갈": 2644, "검단사거리": 2630, "오류동": 2616, "삼동": 2615, "중앙": 2607, "고잔": 2607, "수원시청": 2604, "능곡": 2599, "새절": 2591, "금천구청": 2561, "청명": 2535, "영통": 2535, "구성": 2524, "삼송": 2497, "모란": 2492, "산본": 2492, "수리산": 2492, "백석": 2481, "의왕": 2447, "서동탄": 2427, "매탄권선": 2410, "금정": 2355, "의정부": 2337, "경전철의정부": 2337, "의정부시청": 2337, "흥선": 2337, "의정부중앙": 2337, "도봉산": 2315, "도봉": 2315, "방학": 2311, "쌍문": 2298, "대화": 2282, "상갈": 2262, "구산": 2245, "초지": 2231, "초당": 2199, "세류": 2175, "백운": 2161, "동암": 2161, "동수": 2161, "부평삼거리": 2161, "백마": 2149, "마두": 2149, "가정": 2134, "가정중앙시장": 2134, "양정": 2093, "덕소": 2093, "안암": 2078, "화정": 2034, "주엽": 2034, "대야미": 2002, "곡산": 1997, "완정": 1989, "어정": 1972, "동백": 1972, "한대앞": 1940, "도심": 1935, "강매": 1933, "행신": 1933, "성균관대": 1930, "상동": 1926, "삼산체육관": 1926, "굴포천": 1926, "인천시청": 1926, "예술회관": 1926, "석천사거리": 1926, "부평구청": 1923, "부평": 1923, "부평시장": 1923, "풍산": 1880, "야당": 1879, "운정": 1879, "퇴계원": 1864, "평내호평": 1859, "천마산": 1859, "군포": 1821, "당정": 1821, "세마": 1808, "송내": 1802, "부개": 1802, "주안": 1801, "석바위시장": 1801, "시민공원": 1801, "양평(경의중앙선)": 1798, "검암": 1780, "검바위": 1780, "동오": 1746, "새말": 1746, "박촌": 1744, "북한산우이": 1724, "소래포구": 1708, "인천논현": 1708, "호구포": 1708, "일산": 1701, "장암": 1660, "고진": 1656, "계양": 1651, "귤현": 1651, "동춘": 1617, "동막": 1617, "회룡": 1612, "망월사": 1612, "발곡": 1612, "범골": 1612, "청라국제도시": 1606, "상록수": 1586, "김량장": 1583, "가능": 1575, "녹양": 1575, "탑석": 1564, "곤제": 1564, "어룡": 1564, "송산": 1564, "간석": 1540, "간석오거리": 1540, "제물포": 1533, "도화": 1533, "주안국가산단": 1533, "탄현": 1530, "여주": 1527, "남동인더스파크": 1522, "작전": 1519, "삼가": 1517, "시청·용인대": 1517, "초월": 1517, "인하대": 1495, "신길온천": 1494, "인천가좌": 1466, "가재울": 1466, "용문": 1462, "독정": 1456, "갈산": 1451, "수원": 1441, "지석": 1436, "경기도청북부청사": 1433, "효자": 1433, "원인재": 1426, "연수": 1426, "신연수": 1426, "양수": 1424, "마석": 1419, "덕계": 1399, "달월": 1396, "월곶": 1396, "오이도": 1375, "정왕": 1375, "운서": 1345, "공항화물청사": 1345, "인천국제공항": 1345, "영종": 1345, "송도": 1328, "검단오류": 1314, "금곡": 1300, "덕정": 1282, "계산": 1266, "경인교대입구": 1266, "인천터미널": 1258, "모래내시장": 1255, "만수": 1255, "남동구청": 1255, "인천대공원": 1255, "운동장·송담대": 1254, "선학": 1235, "왕길": 1234, "보평": 1230, "둔전": 1230, "부발": 1218, "이천": 1217, "송탄": 1211, "서정리": 1211, "마전": 1183, "서구청": 1175, "석남": 1175, "서부여성회관": 1175, "숭의": 1152, "오산": 1146, "아시아드경기장": 1122, "명지대": 1116, "곤지암": 1114, "평택": 1083, "금릉": 1065, "금촌": 1065, "임학": 1064, "지행": 912, "가평": 909, "신둔도예촌": 904, "문학경기장": 883, "문산": 823, "전대·에버랜드": 818, "청평": 798, "도원": 742, "동두천": 701, "소요산": 701, "사릉": 690, "인천": 690, "동인천": 690, "신포": 690, "동두천중앙": 673, "보산": 673, "종로3가": 4118, "을지로4가": 4846, "대곡": 2481, "지평": 600, "청계산입구": 6800, "운길산": 1400, "부천": 1759, "중동": 1800, "소사": 1700, "양주": 1000, "원당": 1500, "까치울": 1700, "부천종합운동장": 1700, "춘의": 1500, "신중동": 2200, "부천시청": 2200};

    // ─── 노선별 평균 배차 간격(분, 평일 평시 기준) ────────────
    // 대기시간 = HEADWAY/2 로 반영 (출발 대기 + 환승 시 다음열차 대기)
    const LINE_HEADWAY = {
      "1호선":4, "1호선-경부":6,
      "2호선":2.5, "2호선-성수지선":7, "2호선-신정지선":7,
      "3호선":4, "4호선":4, "5호선":4, "5호선-하남":5,
      "6호선":5, "7호선":4, "8호선":5, "9호선":4.5,
      "경의중앙선":14, "수인분당선":8, "신분당선":5,
      "공항철도":8, "경춘선":20, "우이신설선":6, "신림선":5,
      "GTX-A":20, "김포골드라인":5,
    };
    const waitOf = l => (LINE_HEADWAY[l] ?? 6) / 2;

    // ─── 통근 현실 반영 상수 ──────────────────────────────────
    // RIDE_MULT:   원본 times가 과장된 경향 보정 (공기밀도·진입시간·표정속도 반영)
    // TRANSFER_P:  환승 고정 페널티. 계단·통로·혼잡·실제 대기 편차 반영
    // EXCLUDE_GTX: GTX-A는 요금 프리미엄으로 일반 통근자 이용률이 매우 낮으므로
    //             기본 계산에서 제외하여 네이버의 일반 지하철 경로와 정합시킴
    const RIDE_MULT = 0.9;
    const TRANSFER_P = 2;
    const EXCLUDE_GTX = true;

    // ─── 그래프 ──────────────────────────────────────────────
    // 노드 = "역__노선". 같은 노선 내 역간 이동 비용 = 주행시간(times) * RIDE_MULT.
    // 환승 비용 = 도보시간 + 진입노선 평균 대기(headway/2) + 환승 고정 페널티.
    // 출발 대기 = 출발지에서 선택한 노선의 headway/2 → Dijkstra 초기화 시 반영.
    function buildGraph(){
      const graph={},sl={};
      const addRide=(a,b,t,l)=>{
        const kA=`${a}__${l}`,kB=`${b}__${l}`;
        if(!graph[kA])graph[kA]=[];if(!graph[kB])graph[kB]=[];
        graph[kA].push({node:kB,time:t*RIDE_MULT});
        graph[kB].push({node:kA,time:t*RIDE_MULT});
        if(!sl[a])sl[a]=new Set();if(!sl[b])sl[b]=new Set();
        sl[a].add(l);sl[b].add(l);
      };
      for(const[ln,ld]of Object.entries(LINES_DATA)){
        if(EXCLUDE_GTX && ln==="GTX-A") continue;
        const{stations:s,times:t,circular}=ld;
        for(let i=0;i<s.length-1;i++)addRide(s[i],s[i+1],t[i]||2,ln);
        if(circular)addRide(s[s.length-1],s[0],t[t.length-1]||2,ln);
      }
      // 환승: 도보시간 + 진입 노선의 평균 대기(headway/2) + 환승 페널티
      // 방향성 비용: lA→lB로 갈 때 lB의 대기를, lB→lA로 갈 때 lA의 대기를 더함.
      for(const[st,trs]of Object.entries(TRANSFERS)){
        for(const[lA,lB,walk]of trs){
          if(lB.startsWith("_"))continue;
          if(EXCLUDE_GTX && (lA==="GTX-A" || lB==="GTX-A"))continue;
          const kA=`${st}__${lA}`,kB=`${st}__${lB}`;
          if(graph[kA]&&graph[kB]){
            graph[kA].push({node:kB,time:walk+waitOf(lB)+TRANSFER_P});
            graph[kB].push({node:kA,time:walk+waitOf(lA)+TRANSFER_P});
          }
        }
      }
      return{graph,stationLines:sl};
    }
    function dijkstra(graph,sl,src){
      const srcL=sl[src];if(!srcL)return{};
      const dist={},vis=new Set(),pq=[];
      // 출발 대기 시간을 초기 비용으로 반영 (출발지 후보 노선별)
      for(const l of srcL){const k=`${src}__${l}`;const w=waitOf(l);dist[k]=w;pq.push({node:k,time:w});}
      pq.sort((a,b)=>a.time-b.time);
      while(pq.length){
        const{node,time}=pq.shift();
        if(vis.has(node))continue;vis.add(node);
        if(!graph[node])continue;
        for(const e of graph[node]){
          const nt=time+e.time;
          if(dist[e.node]===undefined||nt<dist[e.node]){dist[e.node]=nt;pq.push({node:e.node,time:nt});pq.sort((a,b)=>a.time-b.time);}
        }
      }
      const sd={};
      for(const[k,t]of Object.entries(dist)){const s=k.split("__")[0];if(sd[s]===undefined||t<sd[s])sd[s]=t;}
      return sd;
    }

    // ─── AI 가격 조회 ─────────────────────────────────────────
    async function fetchPricesForGroup(districtName, stations) {
      const resp = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          model: "claude-sonnet-4-20250514",
          max_tokens: 1000,
          tools: [{ type: "web_search_20250305", name: "web_search" }],
          system: `당신은 부동산 시세 데이터 수집 전문가입니다.
    웹서치로 최신 아파트 평당 매매가를 조사하고, 반드시 순수 JSON만 반환하세요.
    JSON 외 어떤 텍스트도, 마크다운 코드블록도 포함하지 마세요.
    형식: {"역이름": 평당가(만원/평 정수), ...}
    데이터가 없는 역은 포함하지 마세요.`,
          messages: [{
            role: "user",
            content: `${districtName} 지역 아파트 평당 매매가(만원/평)를 웹서치로 조사해주세요. 가장 최신 자료 기준.
    조사 대상 역: ${stations.join(", ")}
    각 역 인근(도보 5~10분) 아파트 단지 실거래가 기준 평당가를 추정해주세요.
    JSON만 반환:`
          }]
        })
      });
      if (!resp.ok) throw new Error(`API ${resp.status}`);
      const data = await resp.json();
      const text = data.content.filter(c => c.type === "text").map(c => c.text).join("").trim();
      const clean = text.replace(/```json|```/g, "").trim();
      return JSON.parse(clean);
    }

    // ─── 등급 ────────────────────────────────────────────────
    const cgr=t=>{
      if(t<=20)return{g:"S",c:"#DC2626",bg:"#FEF2F2"};if(t<=30)return{g:"A",c:"#EA580C",bg:"#FFF7ED"};
      if(t<=40)return{g:"B",c:"#CA8A04",bg:"#FEFCE8"};if(t<=55)return{g:"C",c:"#16A34A",bg:"#F0FDF4"};
      if(t<=75)return{g:"D",c:"#2563EB",bg:"#EFF6FF"};return{g:"F",c:"#7C3AED",bg:"#F5F3FF"};
    };
    const pinfo=p=>{
      if(!p)return{c:"#aaa",bg:"#f5f5f5",label:"정보없음"};
      if(p>=8000)return{c:"#7C3AED",bg:"#F5F3FF",label:"최고가"};if(p>=6000)return{c:"#DC2626",bg:"#FEF2F2",label:"고가"};
      if(p>=4000)return{c:"#EA580C",bg:"#FFF7ED",label:"중고가"};if(p>=2800)return{c:"#CA8A04",bg:"#FEFCE8",label:"중가"};
      if(p>=2000)return{c:"#16A34A",bg:"#F0FDF4",label:"저가"};return{c:"#2563EB",bg:"#EFF6FF",label:"최저가"};
    };
    const calcVS=(t,p)=>{if(!p||t>=9999)return null;return Math.round(Math.max(0,100-t*1.4)*0.5+Math.max(0,100-(p/120))*0.5);};
    const PRICE_RANGES=[{label:"전체",min:0,max:Infinity},{label:"~2,000만",min:0,max:2000},{label:"2~3천만",min:2000,max:3000},{label:"3~5천만",min:3000,max:5000},{label:"5~8천만",min:5000,max:8000},{label:"8천만+",min:8000,max:Infinity}];
    const DEFAULT_HUBS=["광화문","을지로입구","여의도","강남","삼성"];
    const HUB_PRESETS={"주요 업무지구":["광화문","을지로입구","여의도","강남","삼성"],"3대 업무지구":["광화문","여의도","강남"],"강남 클러스터":["강남","역삼","선릉","삼성"],"여의도·마포":["여의도","공덕","홍대입구"],"도심권":["광화문","을지로입구","시청","서울역"]};
    const fmtL=l=>l.replace(/-경부$/,"(경부)").replace(/-성수지선$/,"(성수)").replace(/-신정지선$/,"(신정)").replace(/-하남$/,"(하남)");
    const PAGE=50;const COL="#1a1a2e";

    function App(){
      const[hubs,setHubs]=useState([...DEFAULT_HUBS]);
      const[searchQ,setSearchQ]=useState("");
      const[filterG,setFilterG]=useState("ALL");
      const[prIdx,setPrIdx]=useState(0);
      const[onlyP,setOnlyP]=useState(false);
      const[sk,setSk]=useState("valueScore");
      const[sd2,setSd2]=useState("desc");
      const[showAdd,setShowAdd]=useState(false);
      const[hubSearch,setHubSearch]=useState("");
      const[showPresets,setShowPresets]=useState(false);
      const[page,setPage]=useState(0);
      // 가격 데이터
      const[aiPrices,setAiPrices]=useState({});
      const[fetchState,setFetchState]=useState("idle"); // idle | fetching | done | error
      const[fetchProgress,setFetchProgress]=useState({done:0,total:0,current:""});
      const[lastUpdated,setLastUpdated]=useState(null);
      const[showUpdatePanel,setShowUpdatePanel]=useState(false);

      // 앱 시작 시 캐시 로드 + 자동 갱신 판단
      useEffect(()=>{
        (async()=>{
          try{
            const p = localStorage.getItem("ai-prices");
            const ts = localStorage.getItem("ai-prices-ts");
            if(p)setAiPrices(JSON.parse(p));
            if(ts)setLastUpdated(ts);
            // 30일 이상 지났거나 데이터 없으면 자동 갱신
            const stale=!ts||((Date.now()-new Date(ts).getTime())>30*24*60*60*1000);
            if(stale)autoFetch();
          }catch(e){autoFetch();}
        })();
      },[]);

      const autoFetch=useCallback(async()=>{
        if(fetchState==="fetching")return;
        setFetchState("fetching");
        setFetchProgress({done:0,total:DISTRICT_GROUPS.length,current:""});
        const collected={};
        for(let i=0;i<DISTRICT_GROUPS.length;i++){
          const{name,stations}=DISTRICT_GROUPS[i];
          setFetchProgress({done:i,total:DISTRICT_GROUPS.length,current:name});
          try{
            const prices=await fetchPricesForGroup(name,stations);
            Object.assign(collected,prices);
            // 중간 결과도 즉시 반영
            setAiPrices(prev=>({...prev,...prices}));
          }catch(e){console.warn(`${name} 실패:`,e);}
          await new Promise(r=>setTimeout(r,400));
        }
        const ts=new Date().toISOString();
        try{
          localStorage.setItem("ai-prices",JSON.stringify(collected));
          localStorage.setItem("ai-prices-ts",ts);
          setLastUpdated(ts);
          setAiPrices(collected);
        }catch(e){}
        setFetchState("done");
        setFetchProgress(p=>({...p,done:DISTRICT_GROUPS.length,current:"완료"}));
      },[fetchState]);

      const housingData=useMemo(()=>({...HOUSING_FALLBACK,...aiPrices}),[aiPrices]);
      const{graph,stationLines}=useMemo(()=>buildGraph(),[]);
      const allSt=useMemo(()=>Object.keys(stationLines).sort((a,b)=>a.localeCompare(b,"ko")),[stationLines]);

      const hubDist=useMemo(()=>{
        const d={};for(const h of hubs)d[h]=dijkstra(graph,stationLines,h);return d;
      },[hubs,graph,stationLines]);

      const results=useMemo(()=>allSt.map(st=>{
        const times=hubs.map(h=>{const d=hubDist[h]?.[st];return d!==undefined?d:null;});
        const valid=times.filter(t=>t!==null);
        const avg=valid.length?valid.reduce((a,b)=>a+b,0)/valid.length:Infinity;
        const price=housingData[st]??null;
        return{st,times,avg,cg:cgr(avg),price,pi:pinfo(price),vs:calcVS(avg,price),lines:[...(stationLines[st]||[])]};
      }),[allSt,hubs,hubDist,stationLines,housingData]);

      const filtered=useMemo(()=>{
        const pr=PRICE_RANGES[prIdx];
        let arr=results.filter(r=>r.avg<Infinity);
        if(onlyP)arr=arr.filter(r=>r.price!==null);
        if(searchQ)arr=arr.filter(r=>r.st.includes(searchQ));
        if(filterG!=="ALL")arr=arr.filter(r=>r.cg.g===filterG);
        if(prIdx>0)arr=arr.filter(r=>r.price!==null&&r.price>=pr.min&&r.price<pr.max);
        arr.sort((a,b)=>{
          let c=0;
          if(sk==="avgTime")c=a.avg-b.avg;else if(sk==="price")c=(a.price??99999)-(b.price??99999);
          else if(sk==="valueScore")c=(a.vs??-1)-(b.vs??-1);else c=a.st.localeCompare(b.st,"ko");
          return sd2==="asc"?c:-c;
        });
        return arr;
      },[results,searchQ,filterG,prIdx,onlyP,sk,sd2]);

      const totalP=Math.ceil(filtered.length/PAGE);
      const paged=filtered.slice(page*PAGE,(page+1)*PAGE);
      useEffect(()=>setPage(0),[searchQ,filterG,prIdx,onlyP,sk,sd2]);

      const aiCount=Object.keys(aiPrices).length;
      const pct=fetchProgress.total?Math.round(fetchProgress.done/fetchProgress.total*100):0;

      const thS={padding:"8px 10px",textAlign:"center",fontSize:11,fontWeight:700,color:"#555",whiteSpace:"nowrap",borderBottom:"2px solid #e8e8e8",background:"#f8f9fa"};
      const tdS={padding:"8px 10px",fontSize:12,verticalAlign:"middle",borderBottom:"1px solid #f0f0f0"};

      return(
        <div style={{fontFamily:"'Pretendard','Noto Sans KR',-apple-system,sans-serif",background:"#F4F5F7",minHeight:"100vh",color:COL}}>
          {/* 헤더 */}
          <div style={{background:`linear-gradient(135deg,${COL} 0%,#0f3460 100%)`,padding:"18px 20px 14px",color:"white"}}>
            <div style={{maxWidth:1000,margin:"0 auto"}}>
              <h1 style={{margin:0,fontSize:18,fontWeight:800}}>🏙️ 수도권 지하철 통근 × 주거비용 분석기</h1>
              <div style={{marginTop:6,display:"flex",alignItems:"center",gap:10,flexWrap:"wrap"}}>
                {fetchState==="fetching"?(
                  <div style={{display:"flex",alignItems:"center",gap:8,background:"rgba(255,255,255,.1)",borderRadius:20,padding:"4px 12px"}}>
                    <div style={{width:10,height:10,borderRadius:"50%",background:"#4ade80",animation:"pulse 1s infinite"}}/>
                    <span style={{fontSize:11,opacity:.9}}>
                      AI가 최신 시세 수집 중... {pct}% ({fetchProgress.current})
                    </span>
                  </div>
                ):fetchState==="done"?(
                  <div style={{display:"flex",alignItems:"center",gap:6,fontSize:11,opacity:.75}}>
                    <span>✅ AI 시세 {aiCount}개 역 적용</span>
                    <span>·</span>
                    <span>{lastUpdated?new Date(lastUpdated).toLocaleDateString("ko-KR")+" 기준":""}</span>
                    <button onClick={()=>autoFetch()} style={{fontSize:10,padding:"2px 8px",borderRadius:10,border:"1px solid rgba(255,255,255,.3)",background:"transparent",color:"white",cursor:"pointer",marginLeft:4}}>↻ 새로고침</button>
                  </div>
                ):null}
              </div>
            </div>
          </div>

          {/* 진행 바 */}
          {fetchState==="fetching"&&(
            <div style={{background:"#e8f5e9",borderBottom:"1px solid #c8e6c9",padding:"8px 20px",display:"flex",alignItems:"center",gap:10}}>
              <div style={{flex:1,background:"#e0e0e0",borderRadius:4,height:6,overflow:"hidden"}}>
                <div style={{background:"#16A34A",height:"100%",width:`${pct}%`,transition:"width .4s"}}/>
              </div>
              <span style={{fontSize:11,color:"#555",whiteSpace:"nowrap"}}>{DISTRICT_GROUPS.length}개 지역 중 {fetchProgress.done}개 완료</span>
            </div>
          )}

          <div style={{maxWidth:1000,margin:"0 auto",padding:"12px 14px 40px"}}>

            {/* 허브 패널 */}
            <div style={{background:"white",borderRadius:12,padding:"12px 14px",marginBottom:10,boxShadow:"0 1px 4px rgba(0,0,0,.06)"}}>
              <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:8}}>
                <span style={{fontSize:13,fontWeight:700}}>🚉 허브 역 <span style={{fontWeight:400,color:"#999",fontSize:11}}>({hubs.length}개)</span></span>
                <div style={{display:"flex",gap:5}}>
                  <button onClick={()=>setShowPresets(!showPresets)} style={{fontSize:11,padding:"4px 9px",borderRadius:6,border:"1px solid #ddd",background:"#f7f7f7",cursor:"pointer"}}>프리셋</button>
                  <button onClick={()=>setShowAdd(!showAdd)} style={{fontSize:11,padding:"4px 9px",borderRadius:6,border:"none",background:COL,color:"white",cursor:"pointer"}}>+ 추가</button>
                </div>
              </div>
              {showPresets&&<div style={{display:"flex",flexWrap:"wrap",gap:5,marginBottom:8}}>{Object.keys(HUB_PRESETS).map(n=><button key={n} onClick={()=>{setHubs([...HUB_PRESETS[n]]);setShowPresets(false);}} style={{fontSize:11,padding:"4px 9px",borderRadius:6,border:"1px solid #ddd",background:"#fafafa",cursor:"pointer"}}>{n}</button>)}</div>}
              {showAdd&&(
                <div style={{marginBottom:8}}>
                  <input autoFocus value={hubSearch} onChange={e=>setHubSearch(e.target.value)} placeholder="역 이름 검색..." style={{width:"100%",padding:"7px 11px",fontSize:12,border:"1px solid #ddd",borderRadius:8,outline:"none",boxSizing:"border-box"}}/>
                  {hubSearch&&<div style={{maxHeight:150,overflowY:"auto",border:"1px solid #eee",borderRadius:8,marginTop:3,background:"white"}}>
                    {allSt.filter(s=>s.includes(hubSearch)&&!hubs.includes(s)).slice(0,20).map(s=>(
                      <div key={s} onClick={()=>{if(!hubs.includes(s))setHubs(p=>[...p,s]);setShowAdd(false);setHubSearch("");}} style={{padding:"6px 11px",fontSize:12,cursor:"pointer",borderBottom:"1px solid #f5f5f5",display:"flex",justifyContent:"space-between"}}>
                        <span>{s}</span>
                        <span style={{fontSize:10,color:"#bbb"}}>{housingData[s]?`${housingData[s].toLocaleString()}만/평`:"—"}</span>
                      </div>
                    ))}
                  </div>}
                </div>
              )}
              <div style={{display:"flex",flexWrap:"wrap",gap:6}}>
                {hubs.map(hub=>{const c=LINES_DATA[[...(stationLines[hub]||[])][0]]?.color||"#888";return(
                  <div key={hub} style={{display:"flex",alignItems:"center",gap:5,padding:"4px 10px",borderRadius:8,background:`${c}15`,border:`1px solid ${c}35`,fontSize:13,fontWeight:700,color:c}}>
                    {hub}{hubs.length>1&&<span onClick={()=>setHubs(p=>p.filter(x=>x!==hub))} style={{cursor:"pointer",opacity:.4,fontSize:15,fontWeight:400}}>×</span>}
                  </div>
                );})}
              </div>
            </div>

            {/* 필터 */}
            <div style={{background:"white",borderRadius:12,padding:"12px 14px",marginBottom:10,boxShadow:"0 1px 4px rgba(0,0,0,.06)"}}>
              <div style={{display:"flex",flexWrap:"wrap",gap:7,alignItems:"center",marginBottom:9}}>
                <input value={searchQ} onChange={e=>setSearchQ(e.target.value)} placeholder="🔍 역 이름 검색" style={{flex:"1 1 120px",padding:"7px 10px",fontSize:12,border:"1px solid #e0e0e0",borderRadius:8,outline:"none",minWidth:90}}/>
                <select value={sk+"-"+sd2} onChange={e=>{const[k,d]=e.target.value.split("-");setSk(k);setSd2(d);}} style={{padding:"7px 7px",fontSize:11,borderRadius:8,border:"1px solid #ddd",outline:"none",background:"white"}}>
                  <option value="valueScore-desc">⭐ 가성비 높은순</option>
                  <option value="valueScore-asc">가성비 낮은순</option>
                  <option value="avgTime-asc">통근시간 짧은순</option>
                  <option value="avgTime-desc">통근시간 긴순</option>
                  <option value="price-asc">평당가 낮은순</option>
                  <option value="price-desc">평당가 높은순</option>
                  <option value="station-asc">역명순</option>
                </select>
                <label style={{display:"flex",alignItems:"center",gap:4,fontSize:12,cursor:"pointer",whiteSpace:"nowrap"}}>
                  <input type="checkbox" checked={onlyP} onChange={e=>setOnlyP(e.target.checked)}/>가격 있는 역만
                </label>
              </div>
              <div style={{marginBottom:8}}>
                <div style={{fontSize:10,color:"#aaa",marginBottom:4,fontWeight:700}}>평당 매매가 범위</div>
                <div style={{display:"flex",flexWrap:"wrap",gap:4}}>{PRICE_RANGES.map((pr,i)=>(
                  <button key={i} onClick={()=>setPrIdx(i)} style={{fontSize:11,padding:"3px 8px",borderRadius:5,cursor:"pointer",border:prIdx===i?`1.5px solid ${COL}`:"1px solid #ddd",background:prIdx===i?COL:"#fafafa",color:prIdx===i?"white":"#555",fontWeight:prIdx===i?700:400}}>{pr.label}</button>
                ))}</div>
              </div>
              <div>
                <div style={{fontSize:10,color:"#aaa",marginBottom:4,fontWeight:700}}>통근 등급</div>
                <div style={{display:"flex",flexWrap:"wrap",gap:4}}>{["ALL","S","A","B","C","D","F"].map(g=>{
                  const gc=g==="ALL"?{c:"#555",bg:"#f0f0f0"}:cgr({S:15,A:25,B:35,C:48,D:65,F:80}[g]);
                  return <button key={g} onClick={()=>setFilterG(g)} style={{fontSize:11,padding:"3px 9px",borderRadius:5,cursor:"pointer",border:`1px solid ${gc.c}40`,background:filterG===g?gc.c:gc.bg,color:filterG===g?"white":gc.c,fontWeight:700}}>{g==="ALL"?"전체":g}</button>;
                })}</div>
              </div>
            </div>

            <div style={{fontSize:11,color:"#aaa",marginBottom:6}}>총 {filtered.length}개 역 · {page+1}/{Math.max(1,totalP)}페이지{aiCount>0&&<span style={{color:"#16A34A",marginLeft:8}}>✓ AI 웹서치 시세 {aiCount}개 역 반영</span>}</div>

            {/* 테이블 */}
            <div style={{background:"white",borderRadius:12,boxShadow:"0 1px 4px rgba(0,0,0,.06)",overflowX:"auto"}}>
              <table style={{borderCollapse:"collapse",width:"100%",minWidth:560}}>
                <thead>
                  <tr>
                    <th style={{...thS,width:34}}>#</th>
                    <th style={{...thS,textAlign:"left",minWidth:100}}>역명</th>
                    <th style={{...thS,width:54}}>통근</th>
                    <th style={{...thS,minWidth:90}}>평당 매매가</th>
                    <th style={{...thS,width:62}}>가성비⭐</th>
                    {hubs.map(h=><th key={h} style={{...thS,width:48,fontSize:9}}>{h.length>3?h.slice(0,3)+"…":h}</th>)}
                    <th style={{...thS,width:52}}>평균</th>
                  </tr>
                </thead>
                <tbody>
                  {paged.map((r,i)=>{
                    const rank=page*PAGE+i+1;
                    const isAI=aiPrices[r.st]!==undefined;
                    return(
                      <tr key={r.st} style={{background:i%2?"#fcfcfd":"white"}}>
                        <td style={{...tdS,textAlign:"center",fontSize:10,color:"#ccc",fontWeight:600}}>{rank}</td>
                        <td style={tdS}>
                          <div style={{fontWeight:700,fontSize:13}}>{r.st}</div>
                          <div style={{display:"flex",flexWrap:"wrap",gap:2,marginTop:2}}>
                            {r.lines.slice(0,3).map(l=><span key={l} style={{fontSize:9,padding:"1px 3px",borderRadius:3,background:`${LINES_DATA[l]?.color||"#888"}20`,color:LINES_DATA[l]?.color||"#888",fontWeight:700,whiteSpace:"nowrap"}}>{fmtL(l)}</span>)}
                            {r.lines.length>3&&<span style={{fontSize:9,color:"#bbb"}}>+{r.lines.length-3}</span>}
                          </div>
                        </td>
                        <td style={{...tdS,textAlign:"center"}}>
                          <span style={{display:"inline-block",padding:"2px 6px",borderRadius:5,background:r.cg.bg,color:r.cg.c,fontSize:11,fontWeight:800}}>{r.cg.g}</span>
                        </td>
                        <td style={{...tdS,textAlign:"center"}}>
                          {r.price!==null?(
                            <div>
                              <div style={{fontWeight:700,fontSize:13,color:r.pi.c}}>
                                {r.price.toLocaleString()}만
                                {isAI&&<span title="AI 웹서치 조회" style={{fontSize:8,marginLeft:3,padding:"1px 4px",background:"#e0f2fe",color:"#0369A1",borderRadius:3}}>AI</span>}
                              </div>
                              <div style={{fontSize:9,color:r.pi.c,opacity:.7}}>{r.pi.label}</div>
                            </div>
                          ):(
                            fetchState==="fetching"
                              ?<span style={{fontSize:10,color:"#bbb"}}>조회중...</span>
                              :<span style={{color:"#ddd",fontSize:11}}>—</span>
                          )}
                        </td>
                        <td style={{...tdS,textAlign:"center"}}>
                          {r.vs!==null?(
                            <div style={{display:"flex",flexDirection:"column",alignItems:"center"}}>
                              <div style={{fontSize:15,fontWeight:800,color:r.vs>=70?"#16A34A":r.vs>=50?"#CA8A04":"#DC2626",lineHeight:1}}>{r.vs}</div>
                              <div style={{fontSize:8,color:"#ccc"}}>/100</div>
                            </div>
                          ):<span style={{color:"#ddd",fontSize:11}}>—</span>}
                        </td>
                        {r.times.map((t,j)=>(
                          <td key={j} style={{...tdS,textAlign:"center",fontSize:11,color:t===null?"#ddd":t<=20?"#DC2626":t<=40?"#CA8A04":"#666",fontWeight:t!==null&&t<=20?700:400}}>
                            {t!==null?`${t.toFixed(1)}분`:"—"}
                          </td>
                        ))}
                        <td style={{...tdS,textAlign:"center",fontWeight:800,fontSize:12,color:r.cg.c}}>
                          {r.avg<Infinity?`${r.avg.toFixed(1)}분`:"—"}
                        </td>
                      </tr>
                    );
                  })}
                </tbody>
              </table>
            </div>

            {/* 페이지네이션 */}
            {totalP>1&&(
              <div style={{display:"flex",justifyContent:"center",gap:5,marginTop:12,flexWrap:"wrap"}}>
                {[["«",0],["‹",page-1]].map(([l,p])=>(
                  <button key={l} disabled={page===0} onClick={()=>setPage(Math.max(0,p))} style={{padding:"5px 9px",fontSize:12,borderRadius:6,border:"1px solid #ddd",background:"white",cursor:page===0?"default":"pointer",opacity:page===0?.4:1}}>{l}</button>
                ))}
                {Array.from({length:Math.min(5,totalP)},(_,i)=>{
                  let p;if(totalP<=5)p=i;else if(page<3)p=i;else if(page>totalP-4)p=totalP-5+i;else p=page-2+i;
                  return <button key={p} onClick={()=>setPage(p)} style={{padding:"5px 9px",fontSize:12,borderRadius:6,border:p===page?`1.5px solid ${COL}`:"1px solid #ddd",background:p===page?COL:"white",color:p===page?"white":"#333",cursor:"pointer",minWidth:30}}>{p+1}</button>;
                })}
                {[["›",page+1],["»",totalP-1]].map(([l,p])=>(
                  <button key={l} disabled={page===totalP-1} onClick={()=>setPage(Math.min(totalP-1,p))} style={{padding:"5px 9px",fontSize:12,borderRadius:6,border:"1px solid #ddd",background:"white",cursor:page===totalP-1?"default":"pointer",opacity:page===totalP-1?.4:1}}>{l}</button>
                ))}
              </div>
            )}

            <div style={{marginTop:14,padding:"10px 14px",background:"#EEF1F6",borderRadius:10,fontSize:11,color:"#888",lineHeight:1.7}}>
              COPYRIGHT. 2026 HJ
            </div>
          </div>
          <style>{`@keyframes pulse{0%,100%{opacity:1}50%{opacity:.4}}`}</style>
        </div>
      );
    }

    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<App />);
  </script>
</body>
</html>
