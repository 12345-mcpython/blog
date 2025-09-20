# Markdown 测试

## 二级

### 三级

#### 四级

##### 五级

> 测试

这个页面是对 markdown **静态** 博客的 *测试* (~~测试太长了~~)

\<details\> 无法渲染成功`代码块`

1. test p1
2. test p2
3. test p3

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