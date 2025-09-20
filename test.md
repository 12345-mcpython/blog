# Markdown 测试

## 二级

### 三级

#### 四级

##### 五级

这个页面是对 markdown 静态博客的测试

![img](https://laosun-image.obs.myhuaweicloud.com/20230311215353.png)

```python
from __future__ import annotations

import math
import random
from decimal import Decimal

from .event import Event
from .value import MAIN_ATTRIBUTE, SUB_ATTRIBUTE

position_attributes = {i: list(j.keys())
                       for i, j in MAIN_ATTRIBUTE.read("2").items()}

main_attribute_star_mapping = {2: MAIN_ATTRIBUTE.read("2"), 3: MAIN_ATTRIBUTE.read("3"),
                               4: MAIN_ATTRIBUTE.read("4"),
                               5: MAIN_ATTRIBUTE.read("5")}

sub_attribute_star_mapping = {2: SUB_ATTRIBUTE.read("2"), 3: SUB_ATTRIBUTE.read("3"),
                              4: SUB_ATTRIBUTE.read("4"),
                              5: SUB_ATTRIBUTE.read("5")}

sub_attributes_key_table = list(MAIN_ATTRIBUTE.read("2").keys())


# TODO: add xp
# xp = RELIC_EXP.read()


class Attribute:
    def __init__(self, left: str, right: Decimal, extra: int = None):
        self.left = left
        self.right = right
        self.extra = extra

    def __str__(self):
        return f"<{type(self).__name__} left={self.left} right={self.right} extra={self.extra}>"


class Relic:
    def __init__(
            self,
            event: type[Event],
            level: int,
            name: str,
            position: str,
            star: int,
            main_attribute: Attribute,
            sub_attributes: list[Attribute],
    ):
        self.event = event
        self.level = level
        self.name = name
        self.position = position
        self.star = star
        self.main_attribute = main_attribute
        self.sub_attributes = sub_attributes
        # self.total_xp = sum(xp[str(star)][:level])

    @classmethod
    def generate_random_relic(
            cls, event: type[Event], name: str, position: str, star: int
    ) -> Relic:
        star_main_attributes = main_attribute_star_mapping[str(star)][position]
        star_sub_attributes = sub_attribute_star_mapping[str(star)]
        chosen_main_attribute = random.choice(position_attributes[position])
        chosen_sub_attributes = random.sample(
            sub_attributes_key_table, random.randint(star - 2, star - 1)
        )
        chosen_main_attribute_base = Decimal(str(star_main_attributes[chosen_main_attribute][
            "base"
        ]))
        chosen_sub_attributes_base = [
            Decimal(str(star_sub_attributes[i]["base"])) for i in chosen_sub_attributes
        ]
        main_attribute = Attribute(
            chosen_main_attribute, chosen_main_attribute_base)
        sub_attributes = [
            Attribute(i, j, extra=0)
            for i, j in zip(chosen_sub_attributes, chosen_sub_attributes_base)
        ]
        return cls(event, 0, name, position, star, main_attribute, sub_attributes)

    @classmethod
    def generate_from_json(cls, event: type[Event], position: str, name: int, star: int,
                           relic_json: dict,
                           level: int = 15, verify: bool = True):
        """
        Generate Relic from json
        :param format_version:
        :param verify: check the relics validity
        :param level: relic level
        :param position: relic position "hand" "head" "body" "boot" "ball" "line"
        :param name: name
        :param star: relic star 2,3,4,5
        :param relic_json: {"main_attribute": {"crit_attack": 15},
                 "sub_attributes": {"crit_chance": [3], "health": [3], "defence": [1], "speed": [3,3,3,3,3,3]}}
        3, 3, 3, 3, 3, 3 is the promote level.
        :return: relic class
        """
        main_attribute = relic_json["main_attribute"]
        sub_attributes = relic_json["sub_attributes"]

        main_attribute_left = list(main_attribute.keys())[0]
        main_attribute_right = list(main_attribute.values())[0]
        star_main_attributes = main_attribute_star_mapping[star]
        star_sub_attributes = sub_attribute_star_mapping[star]
        main_attribute_value = star_main_attributes[position][main_attribute_left]["base"] + \
            star_main_attributes[position][main_attribute_left]["bonus"] * int(
            main_attribute_right)

        main_attribute_class = Attribute(
            main_attribute_left, main_attribute_value)
        sub_attributes_class = []
        for key, value in sub_attributes.items():
            sub_attribute_value = star_sub_attributes[key]
            sub_attribute_base = sub_attribute_value["base"]
            sub_attribute_bonus = sub_attribute_value["bonus"]
            sub_attribute = Attribute(key, sub_attribute_base * (value["promote_level"] + 1) + sub_attribute_bonus *
                                      value["attribute_level"], extra=value["promote_level"])
            sub_attributes_class.append(sub_attribute)

        return cls(event, level, name, position, star, main_attribute_class, sub_attributes_class)

    def print(self):
        print("Level: ", self.level)
        print(self.main_attribute.left,
              f"{self.main_attribute.right:.2%}" if self.main_attribute.right < 1 else math.floor(
                  self.main_attribute.right))

        print("sub attributes:")

        for i in self.sub_attributes:
            print(i.left, f"{i.right:.2%}" if i.right <
                  1 else math.floor(i.right), i.extra)


    def __str__(self) -> str:
        return f"<{type(self).__name__} name={self.name} positon={self.position} star={self.star} main_attribute={self.main_attribute} sub_attribute={','.join(str(i) for i in self.sub_attributes)}>"

    def __repr__(self):
        return str(self)


class Relics:
    def __init__(
            self, hand=None, head=None, body=None, boot=None, ball=None, line=None
    ) -> None:
        self.hand: Relic = hand
        self.head: Relic = head
        self.body: Relic = body
        self.boot: Relic = boot
        self.ball: Relic = ball
        self.line: Relic = line
        self.total: list[Relic] = [hand, head, body, boot, ball, line]

    def __str__(self) -> str:
        return f"<{type(self).__name__} hand={str(self.hand)} head={str(self.head)} body={str(self.body)} boot={str(self.boot)} ball={str(self.ball)} line={str(self.line)}>"

    def __repr__(self):
        return str(self)

    def calc_total_value(self):
        total = {}
        for i in self.total:
            if not i:
                continue
            if total.get(i.main_attribute.left):
                total[i.main_attribute.left] += i.main_attribute.right
            else:
                total[i.main_attribute.left] = i.main_attribute.right
            for j in i.sub_attributes:
                if total.get(j.left):
                    total[j.left] += j.right
                else:
                    total[j.left] = j.right
        return total

    def wear(self, relic: Relic):
        match relic.position:
            case "hand":
                self.hand = relic
            case "head":
                self.head = relic
            case "body":
                self.body = relic
            case "boot":
                self.boot = relic
            case "ball":
                self.ball = relic
            case "line":
                self.line = relic
            case _:
                return ValueError(f"Can't wear Position {relic.position}.")

        self.total: list[Relic] = [self.hand, self.head,
                                   self.body, self.boot, self.ball, self.line]
```

```java
package com.laosun;

import com.laosun.aluminium.Battle;
import com.laosun.aluminium.Constant;
import com.laosun.aluminium.Queue;
import com.laosun.aluminium.models.*;
import com.laosun.aluminium.models.Character;

import java.util.List;

public class Main {
    public static void main(String[] args) {
        Character c1 = new Character("test 1", 100, 100, 100, 110);
        Character c2 = new Character("test 2", 200, 200, 100, 150);
        Character c3 = new Character("test 3", 100, 100, 100, 130);
        Character c4 = new Character("test 4", 200, 200, 100, 140);
        Character c5 = new Character("test 5", 100, 100, 100, 120);
        Enemy c6 = new Enemy("test 1", 200, 200, 100, 150);
        Enemy c7 = new Enemy("test 2", 100, 100, 100, 115);
        Enemy c8 = new Enemy("test 3", 200, 200, 100, 135);
        Enemy c9 = new Enemy("test 4", 100, 100, 100, 132);
        Enemy c10 = new Enemy("test 5", 200, 200, 100, 143) {
            @Override
            public void onBattleStart(Battle battle, Moveable moveable) {
                System.out.println("Battle start");
                moveable.move(2000);
            }
        };
        Summon summon = new Summon("test summon 1", 100, 100, 100, 132);
        Queue q = new Queue(List.of(c1, c2, c3, c4, c5), List.of(c6, c7, c8, c9, c10));
        q.add(List.of(summon));
        Battle battle = new Battle(q);
        battle.startBattle();
        battle.getQueue().print();
        System.out.println(Relic.createRandomLevelZero(Relic.Type.BODY, 5));
        String hyaBodyJson = "{ \"main_attribute\": { \"crit_attack\": 15 }, \"sub_attributes\": { \"health_percent\": { \"promote_level\": 3, \"attribute_level\": 5 }, \"speed\": { \"promote_level\": 0, \"attribute_level\": 1 }, \"effect_hit_rate\": { \"promote_level\": 1, \"attribute_level\": 2 }, \"breaking_effect\": { \"promote_level\": 0, \"attribute_level\": 0 } } }";
        String hyaLineJson = "{ \"main_attribute\": { \"energy_regeneration_rate\": 15 }, \"sub_attributes\": { \"defence\": { \"promote_level\": 1, \"attribute_level\": 0 }, \"attack_percent\": { \"promote_level\": 1, \"attribute_level\": 2 }, \"defence_percent\": { \"promote_level\": 1, \"attribute_level\": 2 }, \"effect_resistance\": { \"promote_level\": 1, \"attribute_level\": 0 } } }";
        String hyaBallJson = "{ \"main_attribute\": { \"health_percent\": 15 }, \"sub_attributes\": { \"health\": { \"promote_level\": 0, \"attribute_level\": 0 }, \"defence\": { \"promote_level\": 2, \"attribute_level\": 2 }, \"crit_chance\": { \"promote_level\": 1, \"attribute_level\": 2 }, \"crit_attack\": { \"promote_level\": 2, \"attribute_level\": 4 } } }";
        String hyaBootJson = "{ \"main_attribute\": { \"speed\": 15 }, \"sub_attributes\": { \"attack\": { \"promote_level\": 0, \"attribute_level\": 1 }, \"attack_percent\": { \"promote_level\": 1, \"attribute_level\": 2 }, \"effect_resistance\": { \"promote_level\": 1, \"attribute_level\": 1 }, \"breaking_effect\": { \"promote_level\": 2, \"attribute_level\": 1 } } }";
        String hyaHandJson = "{ \"main_attribute\": { \"attack\": 15 }, \"sub_attributes\": { \"defence\": { \"promote_level\": 1, \"attribute_level\": 1 }, \"health_percent\": { \"promote_level\": 2, \"attribute_level\": 0 }, \"defence_percent\": { \"promote_level\": 0, \"attribute_level\": 2 }, \"speed\": { \"promote_level\": 1, \"attribute_level\": 1 } } }";
        String hyaHeadJson = "{ \"main_attribute\": { \"health\": 15 }, \"sub_attributes\": { \"attack\": { \"promote_level\": 0, \"attribute_level\": 0 }, \"health_percent\": { \"promote_level\": 1, \"attribute_level\": 3 }, \"defence_percent\": { \"promote_level\": 1, \"attribute_level\": 0 }, \"speed\": { \"promote_level\": 2, \"attribute_level\": 2 } } }";
        Relic hyaBody = Relic.createBySetting(Relic.Type.BODY, 5, 15, hyaBodyJson);
        System.out.println(hyaBody);
        Relic hyaLine = Relic.createBySetting(Relic.Type.LINE, 5, 15, hyaLineJson);
        System.out.println(hyaLine);
        Relic hyaBall = Relic.createBySetting(Relic.Type.BALL, 5, 15, hyaBallJson);
        System.out.println(hyaBall);
        Relic hyaBoot = Relic.createBySetting(Relic.Type.BOOT, 5, 15, hyaBootJson);
        System.out.println(hyaBoot);
        Relic hyaHand = Relic.createBySetting(Relic.Type.HAND, 5, 15, hyaHandJson);
        System.out.println(hyaHand);
        Relic hyaHead = Relic.createBySetting(Relic.Type.HEAD, 5, 15, hyaHeadJson);
        System.out.println(hyaHead);
        RelicSuit hya = new RelicSuit();
        hya.addMore(hyaBody, hyaLine, hyaBall, hyaBoot, hyaHand, hyaHead);
        System.out.println(hya.calcTotalValue());
        System.out.println(Constant.WEAPONS.get(23042).name().english());
        System.out.println(Constant.CHARACTERS.get(1409).name().english());
    }
}
```

<details>

```json
[
    {
        "title": "独家解析，昔涟黑厄伏笔！星铁3.3，最顶级细节！",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/3416b35575105093dc0c6665c32f6c11aa8ee482.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114550837875620,
            "epid": 0,
            "bvid": "BV16AJJzDELB",
            "page": 1,
            "cid": 30091510367,
            "part": "独家解析，昔涟黑厄伏笔！星铁3.3，最顶级细节！",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "Jerryprpr",
        "author_face": "https://i2.hdslb.com/bfs/face/3be29be17dd83dda2f5770bc8acfa73ddcc82d6b.jpg",
        "author_mid": 7466789,
        "view_at": 1748666356,
        "progress": 14,
        "badge": "",
        "show_title": "",
        "duration": 533,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114550837875620,
        "tag_name": "手机游戏",
        "live_status": 0
    },
    {
        "title": "这位YouTuber造假100个视频 揭秘全过程 - Yeah Jaron",
        "long_title": "",
        "cover": "http://i2.hdslb.com/bfs/archive/3404d99bcb5fd13adebace37a158e200335f13d6.png",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114586908888086,
            "epid": 0,
            "bvid": "BV1zLjrz3Ej2",
            "page": 1,
            "cid": 30203054823,
            "part": "这位YouTuber造假100个视频 揭秘全过程 - Yeah Jaron",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "AI译片君",
        "author_face": "https://i2.hdslb.com/bfs/face/61f233f90876342b57a39d5b8d031e1cb839461c.jpg",
        "author_mid": 56628194,
        "view_at": 1748666280,
        "progress": 114,
        "badge": "",
        "show_title": "",
        "duration": 1396,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114586908888086,
        "tag_name": "单机游戏",
        "live_status": 0
    },
    {
        "title": "？ ？ ？",
        "long_title": "",
        "cover": "http://i0.hdslb.com/bfs/archive/cba0d312fd0b7a6e97bdf190455b6d1f4932f0a4.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114569628353808,
            "epid": 0,
            "bvid": "BV1sdjVzgEk7",
            "page": 1,
            "cid": 30152001289,
            "part": "？ ？ ？",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "妹足",
        "author_face": "https://i2.hdslb.com/bfs/face/d3418a2e7ea06527863ddaaf684a34a73ae71f68.jpg",
        "author_mid": 1702268237,
        "view_at": 1748665968,
        "progress": 2,
        "badge": "",
        "show_title": "",
        "duration": 61,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114569628353808,
        "tag_name": "同人·手书",
        "live_status": 0
    },
    {
        "title": "“床腐烂”对你的大脑有什么影响？——Psych2Go【中英字幕】",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/b43cb75a148a77513c75ff21725e2ec8af4ec25f.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114464217177489,
            "epid": 0,
            "bvid": "BV17XVtzZEHn",
            "page": 1,
            "cid": 29830284611,
            "part": "“床腐烂”对你的大脑有什么影响？——Psych2Go【中英字幕】",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "志在Peter",
        "author_face": "https://i1.hdslb.com/bfs/face/78599814a4b2601053bb05a7e29a4c9b5d520457.jpg",
        "author_mid": 490569166,
        "view_at": 1748665965,
        "progress": 43,
        "badge": "",
        "show_title": "",
        "duration": 497,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114464217177489,
        "tag_name": "社科·法律·心理",
        "live_status": 0
    },
    {
        "title": "【红太阳】造核弹！凭什么？",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/e8474fdd45963e711ddbd2d9d51467b6f9c67141.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114498241306457,
            "epid": 0,
            "bvid": "BV18X7dzKEJF",
            "page": 1,
            "cid": 29933635474,
            "part": "【红太阳】造核弹！凭什么？",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "星霜喵",
        "author_face": "https://i0.hdslb.com/bfs/face/59a0715e6701b5f4a93f328519c853d1936649d7.jpg",
        "author_mid": 1442646750,
        "view_at": 1748665935,
        "progress": 623,
        "badge": "",
        "show_title": "",
        "duration": 650,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114498241306457,
        "tag_name": "人文历史",
        "live_status": 0
    },
    {
        "title": "1.0时期的一段录屏，这个伤害在那时或许算不低",
        "long_title": "",
        "cover": "http://i2.hdslb.com/bfs/archive/83eacc4da0d6a7bc5563c8938cd566f7d9edb630.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114127649441052,
            "epid": 0,
            "bvid": "BV1PZRWY9ESW",
            "page": 1,
            "cid": 28766635647,
            "part": "1.0时期的一段录屏，这个伤害在那时或许算不低",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "三月七月三",
        "author_face": "https://i2.hdslb.com/bfs/face/3f8e99bd98471df78238e4fd2d098e04848ae90b.jpg",
        "author_mid": 470688026,
        "view_at": 1748665432,
        "progress": -1,
        "badge": "",
        "show_title": "",
        "duration": 36,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114127649441052,
        "tag_name": "手机游戏",
        "live_status": 0
    },
    {
        "title": "乱破从出到现在全都有环境啊！",
        "long_title": "",
        "cover": "http://i0.hdslb.com/bfs/archive/7871c372224cca47b7426f89a400ecc561792776.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114560736432210,
            "epid": 0,
            "bvid": "BV1dHjtzfEHW",
            "page": 1,
            "cid": 30120611222,
            "part": "lv_0_20250523234606",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "XP党捏",
        "author_face": "https://i2.hdslb.com/bfs/face/c914dabcb260df7ec206b6413ec9bdb75ea4a486.jpg",
        "author_mid": 1400304863,
        "view_at": 1748665418,
        "progress": 49,
        "badge": "",
        "show_title": "",
        "duration": 76,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114560736432210,
        "tag_name": "手机游戏",
        "live_status": 0
    },
    {
        "title": "《有求也不应的一集》",
        "long_title": "",
        "cover": "http://i0.hdslb.com/bfs/archive/a2c21301c6c0accda8921f5137a4233d17eefaee.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114548908494790,
            "epid": 0,
            "bvid": "BV1gQJLzaEXL",
            "page": 1,
            "cid": 30083580737,
            "part": "《有求也不应的一集》",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "Narwalncy",
        "author_face": "https://i1.hdslb.com/bfs/face/7c8bd8bd4de7f6450112e67ad472a300293791fb.jpg",
        "author_mid": 34925014,
        "view_at": 1748665414,
        "progress": 39,
        "badge": "",
        "show_title": "",
        "duration": 108,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114548908494790,
        "tag_name": "手机游戏",
        "live_status": 0
    },
    {
        "title": "服 主 被 砍 了 ！",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/311eece9b6a5be7f2c3e9a109912a522e83eacf7.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114594391526479,
            "epid": 0,
            "bvid": "BV1Yq7Jz6ELd",
            "page": 1,
            "cid": 30222519530,
            "part": "服 主 被 砍 了 ！",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "不风采的琴",
        "author_face": "https://i0.hdslb.com/bfs/face/3b9d26706a8c3ab599a427f6fa2f7d1547d96b69.jpg",
        "author_mid": 311681310,
        "view_at": 1748665344,
        "progress": 96,
        "badge": "",
        "show_title": "",
        "duration": 275,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114594391526479,
        "tag_name": "MMD·3D",
        "live_status": 0
    },
    {
        "title": "我们的上一秒去哪了？从狭义相对论到广义相对论，理解时间究竟是什么",
        "long_title": "",
        "cover": "http://i0.hdslb.com/bfs/archive/7a4b7b715f4f2650d4b0a4ed5759979b905238f9.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 793870274,
            "epid": 0,
            "bvid": "BV1oC4y1k7iT",
            "page": 1,
            "cid": 1408717315,
            "part": "我们的上一秒去哪了？从狭义相对论到广义相对论，理解时间究竟是什么",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "想象研究所",
        "author_face": "https://i0.hdslb.com/bfs/face/e32aa661397c80e9c7542142a053d323dc645af2.jpg",
        "author_mid": 1914881574,
        "view_at": 1748665227,
        "progress": 414,
        "badge": "",
        "show_title": "",
        "duration": 1486,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 793870274,
        "tag_name": "科学科普",
        "live_status": 0
    },
    {
        "title": "人类为什么会演化出这么长的睡眠时间？",
        "long_title": "",
        "cover": "http://i0.hdslb.com/bfs/archive/0687ecf81d87dec598d33910d65f2e3bb1717752.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114538540179329,
            "epid": 0,
            "bvid": "BV1pXJtzJEeF",
            "page": 1,
            "cid": 30051926994,
            "part": "人类为什么会演化出这么长的睡眠时间？",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "行者道荣",
        "author_face": "https://i0.hdslb.com/bfs/face/6c95d8ff72c0de9720266c23d7a5646a4f00bf10.jpg",
        "author_mid": 3537118582278552,
        "view_at": 1748665070,
        "progress": 147,
        "badge": "",
        "show_title": "",
        "duration": 261,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114538540179329,
        "tag_name": "科学科普",
        "live_status": 0
    },
    {
        "title": "1+1风堇一创已经325w了，还不明白吗这根本就不是奶妈",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/5ac191457b95dc236ec03985d5ceca5568b17aa7.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114547063002942,
            "epid": 0,
            "bvid": "BV1r9J5z3EAa",
            "page": 1,
            "cid": 30081419454,
            "part": "1+1风堇一创已经325w了，还不明白吗这根本就不是奶妈",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "huoChai_27",
        "author_face": "https://i2.hdslb.com/bfs/face/8059c44b34e4054625d2f998652969bf732e00c3.jpg",
        "author_mid": 3202472,
        "view_at": 1748664816,
        "progress": 55,
        "badge": "",
        "show_title": "",
        "duration": 172,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114547063002942,
        "tag_name": "手机游戏",
        "live_status": 0
    },
    {
        "title": "40年前IBM的第一台便携PC用上了DeepSeek",
        "long_title": "",
        "cover": "http://i0.hdslb.com/bfs/archive/7689ee9f6e883daf36924a79e2676c16b1646310.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114592478925340,
            "epid": 0,
            "bvid": "BV1W97LzVEsX",
            "page": 1,
            "cid": 30219502996,
            "part": "这是IBM的第一台便携PC，命运石让它连上了DeepSeek！",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "GeekLogic",
        "author_face": "https://i2.hdslb.com/bfs/face/fc4ba4087efa80dce11dfdd1c2bc07d84fb603b9.jpg",
        "author_mid": 1347373376,
        "view_at": 1748664815,
        "progress": 58,
        "badge": "",
        "show_title": "",
        "duration": 1826,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114592478925340,
        "tag_name": "数码",
        "live_status": 0
    },
    {
        "title": "不是那你就说是不是希儿0T吧你就",
        "long_title": "",
        "cover": "http://i0.hdslb.com/bfs/archive/3d6f45c5e99d0781358df801369987d773d9214b.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114551475411702,
            "epid": 0,
            "bvid": "BV1W6JHzPE76",
            "page": 1,
            "cid": 30093479168,
            "part": "希儿玩家精神状态查询",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "不疯真君",
        "author_face": "https://i2.hdslb.com/bfs/face/3dd7249be1f2cea688851138bfdc0d525895d8b6.jpg",
        "author_mid": 353530283,
        "view_at": 1748664815,
        "progress": 22,
        "badge": "",
        "show_title": "",
        "duration": 166,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114551475411702,
        "tag_name": "网络游戏",
        "live_status": 0
    },
    {
        "title": "现在还玩黄泉跟流萤真的是坚持了！",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/30a99ae7a669e310fb0a00894f3f4e695114cfcc.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114595431848721,
            "epid": 0,
            "bvid": "BV1bG7HziEtq",
            "page": 1,
            "cid": 30226058986,
            "part": "lv_0_20250530143845",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "XP党捏",
        "author_face": "https://i2.hdslb.com/bfs/face/c914dabcb260df7ec206b6413ec9bdb75ea4a486.jpg",
        "author_mid": 1400304863,
        "view_at": 1748664815,
        "progress": 2,
        "badge": "",
        "show_title": "",
        "duration": 53,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114595431848721,
        "tag_name": "手机游戏",
        "live_status": 0
    },
    {
        "title": "您有一份全新首页，请注意查收~",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/4f863fe9b99795fc159bb77feb7abb26788a8180.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114592160157574,
            "epid": 0,
            "bvid": "BV1Tp7jzWErx",
            "page": 1,
            "cid": 30218322347,
            "part": "您有一份全新首页，请注意查收~",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "中文Minecraft_Wiki",
        "author_face": "https://i1.hdslb.com/bfs/face/62adc3ec112bca755e41336b33f7ab705309a64d.jpg",
        "author_mid": 487789834,
        "view_at": 1748664815,
        "progress": 9,
        "badge": "",
        "show_title": "",
        "duration": 60,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114592160157574,
        "tag_name": "软件应用",
        "live_status": 0
    },
    {
        "title": "原来白厄早就知道了打破轮回的关键！他还解释了到底为什么要送主角二人离开",
        "long_title": "",
        "cover": "http://i0.hdslb.com/bfs/archive/2282da392504a482404ecd2a11000ccd11e78b67.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114585818367638,
            "epid": 0,
            "bvid": "BV1vnjzzFEQD",
            "page": 1,
            "cid": 30199646081,
            "part": "原来白厄早就知道了打破轮回的关键！他还解释了到底为什么要送主角二人离开",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "冰夜彻为雾",
        "author_face": "https://i2.hdslb.com/bfs/face/91a62b103d5f06eda23c86fd6a2c5f007c450785.jpg",
        "author_mid": 1267591712,
        "view_at": 1748664815,
        "progress": 59,
        "badge": "",
        "show_title": "",
        "duration": 74,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114585818367638,
        "tag_name": "网络游戏",
        "live_status": 0
    },
    {
        "title": "奶奶为十年前的我录制的Mc视频",
        "long_title": "",
        "cover": "http://i2.hdslb.com/bfs/archive/915da28a25d39a1be9109efc7d0443aaf305dc58.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114552582704180,
            "epid": 0,
            "bvid": "BV1YnJWzREj6",
            "page": 1,
            "cid": 30098132476,
            "part": "奶奶为十年前的我录制的Mc视频",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "湖宝",
        "author_face": "https://i2.hdslb.com/bfs/face/464879271ecfffd9452eeef9003f4e969b78f8cf.png",
        "author_mid": 101000036,
        "view_at": 1748664815,
        "progress": 28,
        "badge": "",
        "show_title": "",
        "duration": 61,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114552582704180,
        "tag_name": "单机游戏",
        "live_status": 0
    },
    {
        "title": "如果有朋友想入坑崩铁记得极力劝阻！当花瓶看看就好",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/2fc599c0f09f810d874255b5b537278d548cbf0d.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114580701387906,
            "epid": 0,
            "bvid": "BV1WojozbEfA",
            "page": 1,
            "cid": 25803300133,
            "part": "有好兄弟想入坑这游戏记得极力劝阻！当花瓶看看就好",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "雀门琦琦",
        "author_face": "https://i2.hdslb.com/bfs/face/73ffb79a07e85a30abe5b8f409864e59796535c6.jpg",
        "author_mid": 2068141799,
        "view_at": 1748664815,
        "progress": 70,
        "badge": "",
        "show_title": "",
        "duration": 291,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114580701387906,
        "tag_name": "手机游戏",
        "live_status": 0
    },
    {
        "title": "[星铁手术76]爽凹俩小时！从未猜到最后MVP竟然是。。。！？",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/c06f0cfb4a214fc822a1e5e180ab8391bbbec69e.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114586791452553,
            "epid": 0,
            "bvid": "BV1mcjrzDEus",
            "page": 1,
            "cid": 30202921886,
            "part": "[星铁手术76]爽凹俩小时！从未猜到最后MVP竟然是。。。！？",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "男人的承诺",
        "author_face": "https://i0.hdslb.com/bfs/face/bb9fee691230d99aa6fa013cf80a45bfe7ec91cd.jpg",
        "author_mid": 81371985,
        "view_at": 1748664815,
        "progress": 9,
        "badge": "",
        "show_title": "",
        "duration": 2210,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114586791452553,
        "tag_name": "手机游戏",
        "live_status": 0
    },
    {
        "title": "真的坚持不下去了，准备退坑鸣潮转米了",
        "long_title": "",
        "cover": "http://i0.hdslb.com/bfs/archive/ded1fc49150c3497623804baba40efbaa1db97f3.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114575315836238,
            "epid": 0,
            "bvid": "BV1PrjUzqEe3",
            "page": 1,
            "cid": 25802906704,
            "part": "lv_0_20250527011336",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "loGNim",
        "author_face": "https://i2.hdslb.com/bfs/face/1a028ece1b3d4d69dd6e57ddd6e244495aafa259.jpg",
        "author_mid": 585204497,
        "view_at": 1748664814,
        "progress": 79,
        "badge": "",
        "show_title": "",
        "duration": 112,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114575315836238,
        "tag_name": "手机游戏",
        "live_status": 0
    },
    {
        "title": "从评论区挑选一张图片喂给AI500次会发生什么(16)",
        "long_title": "",
        "cover": "http://i2.hdslb.com/bfs/archive/2b11736e64c4ee910350cdd109feea2eacc3bf54.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114581070416927,
            "epid": 0,
            "bvid": "BV1grjozzEb9",
            "page": 1,
            "cid": 30186275252,
            "part": "从评论区挑选一张图片喂给AI500次会发生什么(16)",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "癫外飞仙",
        "author_face": "https://i0.hdslb.com/bfs/face/da237f256d22327e5033084797b0948a24fc744e.jpg",
        "author_mid": 3546658140850611,
        "view_at": 1748664814,
        "progress": 39,
        "badge": "",
        "show_title": "",
        "duration": 45,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114581070416927,
        "tag_name": "绘画",
        "live_status": 0
    },
    {
        "title": "腾的大手！Q币现在能充到鸣潮里去了？！",
        "long_title": "",
        "cover": "http://i0.hdslb.com/bfs/archive/ff3553b408a1636a1b0dfee69d145d0f174baee4.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114556558902977,
            "epid": 0,
            "bvid": "BV1K1jnzcEZC",
            "page": 1,
            "cid": 30105208853,
            "part": "腾的大手！Q币现在能充到鸣潮里去了？！",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "君不野",
        "author_face": "https://i2.hdslb.com/bfs/face/45731468882675bfa3da02287c735ac5cf377ff9.jpg",
        "author_mid": 366084233,
        "view_at": 1748664814,
        "progress": 107,
        "badge": "",
        "show_title": "",
        "duration": 115,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114556558902977,
        "tag_name": "手机游戏",
        "live_status": 0
    },
    {
        "title": "内存虚拟当硬盘（不留垃圾又健康）",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/0e72dbb628a621c1aff1931a2f7c39d3335eed61.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114562162491732,
            "epid": 0,
            "bvid": "BV1ptjbzyEnT",
            "page": 1,
            "cid": 30126114215,
            "part": "内存虚拟当硬盘（不留垃圾又健康）",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "lovdge",
        "author_face": "https://i1.hdslb.com/bfs/face/4ac229c5ddf606bf9fbd709d7a3a269f5f22b810.jpg",
        "author_mid": 8873749,
        "view_at": 1748664814,
        "progress": 123,
        "badge": "",
        "show_title": "",
        "duration": 243,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114562162491732,
        "tag_name": "计算机技术",
        "live_status": 0
    },
    {
        "title": "真的很讨厌这个角色（傲娇版）（补档）",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/66845b6f8c1d1f45c45bdefa2e7dabdc5312cd03.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114575399715182,
            "epid": 0,
            "bvid": "BV1XPjSzDE8L",
            "page": 1,
            "cid": 30169301497,
            "part": "真的很讨厌这个角色",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "米白Yume",
        "author_face": "https://i1.hdslb.com/bfs/face/243cb141d90318d620994373c5182c941f8ac903.jpg",
        "author_mid": 8283835,
        "view_at": 1748664814,
        "progress": 14,
        "badge": "",
        "show_title": "",
        "duration": 124,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114575399715182,
        "tag_name": "手机游戏",
        "live_status": 0
    },
    {
        "title": "[星铁手术75]月卡党攒满1000抽！自己的号打完这期再也不会痛苦了！！",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/eaf6365ed69728b614a1f6840e2973c9e6ff0e06.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114581171084729,
            "epid": 0,
            "bvid": "BV1hJjYziE3P",
            "page": 1,
            "cid": 30186407076,
            "part": "[星铁手术75]月卡党攒满1000抽！自己的号打完这期再也不会痛苦了！！",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "男人的承诺",
        "author_face": "https://i0.hdslb.com/bfs/face/bb9fee691230d99aa6fa013cf80a45bfe7ec91cd.jpg",
        "author_mid": 81371985,
        "view_at": 1748664814,
        "progress": 89,
        "badge": "",
        "show_title": "",
        "duration": 1575,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114581171084729,
        "tag_name": "手机游戏",
        "live_status": 0
    },
    {
        "title": "查！往前倒600年也要查！找！用直升机上天也要找！",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/c989c44c8c7423a448581489b71404aebcf9f9b5.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114499684147107,
            "epid": 0,
            "bvid": "BV15hEczdEiv",
            "page": 1,
            "cid": 29938680096,
            "part": "查！往前倒600年也要查！找！用直升机上天也要找！",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "野人少女79",
        "author_face": "https://i0.hdslb.com/bfs/face/f7b1b6f2ce7b92fd435c1318191b93f31e3bfc29.jpg",
        "author_mid": 1249029768,
        "view_at": 1748664814,
        "progress": 774,
        "badge": "",
        "show_title": "",
        "duration": 847,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114499684147107,
        "tag_name": "人文历史",
        "live_status": 0
    },
    {
        "title": "风堇：超市里的…小伊卡？【菈崩铁】",
        "long_title": "",
        "cover": "http://i0.hdslb.com/bfs/archive/762b20d7808824e00c742c4da6a7e08bb635e25c.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114538087192556,
            "epid": 0,
            "bvid": "BV1PBEfzTE16",
            "page": 1,
            "cid": 25799958050,
            "part": "风堇：超市里的…小伊卡？【菈崩铁】",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "宝菈卟妞BOOM",
        "author_face": "https://i1.hdslb.com/bfs/face/eb39fd7d649695d6af74c838975b3f373a19823e.jpg",
        "author_mid": 3493285567925098,
        "view_at": 1748664814,
        "progress": 1,
        "badge": "",
        "show_title": "",
        "duration": 88,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114538087192556,
        "tag_name": "手机游戏",
        "live_status": 0
    },
    {
        "title": "无效社交，会把你吃干抹净",
        "long_title": "",
        "cover": "http://i1.hdslb.com/bfs/archive/e5c08c54895993e630030407e7ecc720c387e7fa.jpg",
        "covers": null,
        "uri": "",
        "history": {
            "oid": 114555451675288,
            "epid": 0,
            "bvid": "BV17PjJzgEpX",
            "page": 1,
            "cid": 30103636796,
            "part": "无效社交，会把你吃干抹净",
            "business": "archive",
            "dt": 2
        },
        "videos": 1,
        "author_name": "爱戏剧的柏拉图",
        "author_face": "https://i2.hdslb.com/bfs/face/c0829b644f312828e023d202f8535b41c695d776.jpg",
        "author_mid": 283659872,
        "view_at": 1748664814,
        "progress": 193,
        "badge": "",
        "show_title": "",
        "duration": 550,
        "current": "",
        "total": 0,
        "new_desc": "",
        "is_finish": 0,
        "is_fav": 0,
        "kid": 114555451675288,
        "tag_name": "社科·法律·心理",
        "live_status": 0
    }
]
```

</details>