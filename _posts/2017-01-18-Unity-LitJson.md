---
layout: post
title: 유니티 Json 사용하기. LitJson편
description: Json
modified: 2017-01-18
tags: [programming], [LitJson]
categories: [study]
---

---

1. 목차

   {:toc}

---

# Overview

유니티에서 json을 사용할 수 있게 해주는 라이브러리가 많다. 그 많은 라이브러리 중 가장 많이 보이는 라이브러리인 **LitJson**에 대해서 저장용으로 이 글을 작성한다.

더 많은 라이브러리에 대한 자세한 내용은 [링크](https://heartgamer.wordpress.com/2015/01/10/unity%EC%97%90%EC%84%9C-json-%EC%82%AC%EC%9A%A9-%EC%8B%9C-%EA%B3%A0%EB%A0%A4%ED%95%A0-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC/)를 참조하자.

## LitJson

[LitJson](http://lbv.github.io/litjson/) 에서 자세한 설명과 다운로드를 받자.

다른 라이브러리들과 마찬가지로 ios에서 **Serialize**에 문제가 있다고 한다. [해결법](http://forum.unity3d.com/threads/litjson-issue-on-ios.113181) 참고하자.

### 사용법

Json파일의 내용물이다.

```json
[
    {
        "id": 0,
        "title": "알제리",
        "slug": "alg",
        "continent": "africa"
    },
    {
        "id": 1,
        "title": "앙골라",
        "slug": "ang",
        "continent": "africa"
    }
]
```

**StreamingAssets** 폴더에 Json파일을 넣도록하자.

Json파일을 불러오자.

```c#
using LitJson;

public class TestJson : MonoBehaniour
{
  private List<Database> lDatabase = new List<Database>();
  private List<Item> database = new List<Item>();
  private JsonData itemData;
  
  class Database
	{
		public string code {get; set;}
		public List<Item> databaseList = new List<Item>();
		public JsonData flagData {get; set;}

		public void DataListIn(Item item)
		{
			databaseList.Add(item);
		}

		public Database()
		{
			databaseList = new List<Item>();
		}
	}
  
 public class Item
	{
	public int ID {get; set;}
	public string Title {get; set;}
	public string Continent {get; set;}
	public string Slug {get; set;}
	public Sprite Sprite {get; set;}

	public Item(int id, string title, string slug, string continent)
	{
		this.ID = id;
		this.Title = title;
		this.Continent = continent;
		this.Slug = slug;
		if (this.Continent == "medal")
		{
			this.Sprite = Resources.Load<Sprite>("Sprites/Medal/" + slug);
		}
		else if (this.Continent == "filter")
		{
			this.Sprite = Resources.Load<Sprite>("Sprites/Filter/" + slug);
		}
		else
			this.Sprite = Resources.Load<Sprite>("Sprites/Nation/" + continent + "/" + slug);
	}
  
  	void Awake()
    {
      JsonLoad();
    }
  
  ///제이슨 오픈 스타트
	void JsonLoad()
	{
		for (int i = 0; i < dataPath.Length; i++)
		{
			Database data = new Database();

			data.flagData = JsonMapper.ToObject(File.ReadAllText(Application.streamingAssetsPath + "/" + dataPath[i]));
			data.code = dataCode[i];

			ConstructItemDatabase(data);
		}
	}
  
  ///json 파일에서 정보 빼오기
	void ConstructItemDatabase(Database database)
	{
		for (int i = 0; i < database.flagData.Count; i++)
		{
			database.DataListIn(new Item((int)database.flagData[i]["id"], database.flagData[i]["title"].ToString(),
			 database.flagData[i]["slug"].ToString(), database.flagData[i]["continent"].ToString()));
		}

		continentDatabase.Add(database);
	}
}
```