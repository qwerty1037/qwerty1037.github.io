---
layout: single
title: "[flutter] 검색창 구현"
---

### 1. 클래스와 변수 생성

---

검색창을 구현할 class SearchBar를 생성하고, 사용할 변수들을 정의해준다.  
각 변수들에 대한 설명은 아래와 같다.

`_filter`: Textfield를 관리할 TextEditingController

`names`: 검색의 대상이 되는 전체 list

`filteredNames`: 검색창에 의해 filtered된 list

`_searchIcon`: appbar의 leading parameter에 들어갈 아이콘으로, Icons.search로 초기화

`_appbarTitle`: appbar에 들어갈 기본 메세지

```dart
class SearchBar extends StatefulWidget {
  const SearchBar({Key key}) : super(key: key);

  @override
  _SearchBarState createState() => _SearchBarState();
}

class _SearchBarState extends State<SearchBar> {

  final TextEditingController _filter = new TextEditingController(); // 필터

  String _searchText = ""; //검색창에 입력한 text

  List names = []; // dataclass.name 리스트

  List filteredNames = []; // searchtext에 의해 필터링된 name

  Icon _searchIcon = Icon(Icons.search); //검색 아이콘

  Widget _appBarTitle = Text('Search Example'); // Search Example
```

---

### 2. data를 가져오는 함수 생성

---

원하는 데이터를 names, filteredNames에 할당하는 초기화 함수를 생성한다.  
이는 앱을 처음 실행할 때, \_searchText가 비어있을 때 실행된다.

```dart
  //리스트를 변수로 가져오는 함수
  void _getNames () async {
    // TODO: dataclass에서 list를 뽑는 코드가 추가되어야 함
    setState(() {
      names = datalist;
      filteredNames = datalist;
    });
  }
  @override
  void initState(){
    _getNames();
    super.initState();
  }

```

---

### 3. appbar 구현

---

검색창 ui를 구현한 build widget을 생성한다.  
leading parameter에 있는 icon을 이용해 사용자가 검색창을 활성화시켰는지 판단한다.  
이를 위해 \_searchPressed함수를 생성한다.

```dart
Widget build(BuildContext context) {
    return Scaffold(
      appBar: _buildBar(context),
      body: Container(
        child: _buildList(),
      ),
    );
  }

Widget _buildBar(BuildContext context) {
  return AppBar(
    centerTitle: true,
    title: _appBarTitle,
    leading: IconButton(
      icon: _searchIcon,
      onPressed: _searchPressed,
    ),
  );
}
```

`onPressed: _searchPressed`: \_searchPressed 함수는 검색창의 활성화와 비활성화를 담당한다. on-off switch라고 생각하면 이해가 쉽다.  
검색창의 활성화/비활성화 여부를 \_searchIcon의 종류로 판단하게 된다.  
`if(_searchIcon.icon == Icons.search)`: Icons.search는 검색창이 비활성화되었을 때 appbar의 leading에 나타날 아이콘으로, "검색창이 비활성화되었을 때"를 의미한다. 아이콘을 누르게 되면 검색창을 활성화한다.  
`else`: 활성화된 검색창을 비활성화하는 구문이다.

```dart
void _searchPressed() {
    setState(() {
      if(_searchIcon.icon == Icons.search) { //off->on
        _searchIcon = Icon(Icons.close);
        _appBarTitle = TextField(
          controller: _filter,
          decoration: InputDecoration(
            prefixIcon: Icon(Icons.search),
            hintText: 'Search...'
          ),
        );
      }
      else { //on->off
        _searchIcon = Icon(Icons.search);
        _appBarTitle = Text('Search Example');
        filteredNames = names;
        _filter.clear();
      }
    });
  }
```

---

### 4. TextEditingController에 listener 부착

---

이부분은 아직 제대로 이해하지 못했다.  
StatefulWidget의 lifecycle을 아직 완벽히 이해하지 못해서 생성자가 어떤 때에 호출되는지 잘 모르고, TextEditingController와 listener을 이해하지 못했다.  
이부분은 추가로 공부 후 포스팅을 수정하거나, 재포스팅할 예정이다.

```dart
_SearchBarState(){
    _filter.addListener(() {
      if (_filter.text.isEmpty) {
        setState(() {
          _searchText = "";
          filteredNames = names;
        });
      }
      else {
        setState(() {
          _searchText = _filter.text;
        });
      }
    });
  }
```

---

### 5. 검색 알고리즘 구현

마지막으로, 검색 알고리즘을 구현하기 위해 패키지 `korea_regexp` 를 설치한다.

---

링크: <https://pub.dev/packages/korea_regexp>

```dart
dependencies:
  korea_regexp: ^0.0.2
```

_pubspec.yaml 파일에 위 구문을 추가하고 pub get_

```dart
import 'package:korea_regexp/get_regexp.dart';
import 'package:korea_regexp/models/regexp_options.dart';
```

_패키지를 import_

---

위 패키지는 정규 표현식을 이용해 문자열의 일치를 확인한다.  
이를 위해 `regExp`라는 변수를 사용한다. 패키지에 대한 설명은 위 링크를 참조하면 된다.  
`_searchText`와 `names`를 대조해, 일치하는 index만 `tempList`에 추가해 `filteredNames`에 넣는다.  
그후, `filteredNames`를 출력한다.

```dart
Widget _buildList () {
  if (!(_searchText.isEmpty)) { //무언가를 입력했다면
    List tempList = [];
    RegExp regExp = getRegExp(
      _searchText,
      RegExpOptions(
        initialSearch: true,
        startsWith: false,
        endsWith: false,
        fuzzy: true,
        ignoreSpace: true,
        ignoreCase: true,
      )
    );
    for (int i=0;i<names.length;i++) {
      if(regExp.hasMatch(names[i])) { //입력한 문자와 일치하는 검색 결과 필터
        tempList.add(names[i]);
      }
    }
    filteredNames = tempList;
  }
  return ListView.builder(
    itemCount: names == null? 0 : filteredNames.length,
    itemBuilder: (BuildContext context, int index) {
      return ListTile(
        title: Text(filteredNames[index]),
        // ignore: avoid_print
        onTap: () => print(filteredNames[index]),
      );
    },
  );
}
```
