+++
title = "Firedac에서 SQLite 사용하기(Window)"
author = ["Hillfolk"]
date = 2019-02-24
lastmod = 2019-02-26T01:19:24+09:00
tags = ["Delphi", "Firedac", "Database"]
categories = ["posts"]
weight = 100
draft = true
+++

## Firedac에서 SQLite 사용하기(Window) {#firedac에서-sqlite-사용하기--window}

firedac은 델파이에서 기본적으로 제공하는 데이터베이스 엑서스 컴포넌트입니다. Window 나 MacOS 또는 모바일에서 다 사용이 가능하기 때문에 최근에는 가장 쉽게 접근할수 있는 컴포넌트입니다. 회사에서 프로젝트를 진행하면서 로컬에서 데이터베이스를 사용해야 하기 때문에 가벼운 SQLite 를 사용해야 할때 쉽게 설치가 가능하기 때문에 사용해봤습니다.  그러면서 FireDac에서 SQLite 를 사용하는 방법에 대해서 간단하게 포스트 남깁니다.


### FireDac에서 SQLite3 연결하기 {#firedac에서-sqlite3-연결하기}

우선 연결을 위해서는 Driver 를 설정해야 합니다. 그리고  TFDPhysSQLiteDriverLink 를 Form에 올려 놓고 VenderHome 와 VenderLib 를 지정합니다. 저늘 실행파일이 실행되는 곳에서 SQLite3 폴더를 만들고 그안에 DLL 파일을 넣었습니다. 컴포넌트에 아래와 같이 지정해 줍니다.

```nil
VenderHome: .\Sqlite3
VenderLib: .\Sqlite3\sqlite3.dll
DriverID : SQLite
```


### 데이터베이스 연결하기 {#데이터베이스-연결하기}

TFDConnection 컴포넌트를 이용해서 데이터베이스를 연결할수 있습니다. FDConnection에서 Params 에 설정되는 항목은 아래와 같습니다.  최소 아래 두가지 항목을 설정해주시면 사용이 가능합니다.

```nil
with FDConnection1 do
begin
  Params.Values['DriverID'] := 'SQLite';
  Params.Values['Database'] := edDBPath.Text + 'demo.sdb';
end;
FDConnection1.Connected := True;
```


### 데이터베이스 쿼리사용하기 {#데이터베이스-쿼리사용하기}

FDQuery 나 FDTable 컴포넌트를 이용하여 데이터베이스를 컨트롤 할수 있습니다.
아래와 같이 테이블 컴포넌트를 이용해서 조회,수정,삭제 등의 작업을 할수 있습니다.

```nil

FDTable1.SQL.Clear;
SQL := '';
SQL := format('INSERT INTO FDPERSION VALUES("%s",%s,%d);',
  [edID.Text, edName.Text, StrToInt(edAGE.Text)]);
FDTable1.SQL.Add(SQL);
FDTable1.ExecSQL;
FDTable1.Open;

```

자세한 사항을 아래 사이트를 참고하시면 도움이 됩니다. ^^

[Source](https://github.com/hillfolk/FiredacSqlite)
[embarcadero](http://docwiki.embarcadero.com/RADStudio/Berlin/en/Connect_to_SQLite_database_(FireDAC) )