---
title: Pyhton标准库collections.namedtuple
date: 2018-03-05 09:24:15
tags:
---
```python
import collections
from random import choice

"""
# 先挖个坑在这里挨个来吧
* namedtuple   factory function for creating tuple subclasses with named fields
* deque        list-like container with fast appends and pops on either end
* ChainMap     dict-like class for creating a single view of multiple mappings
* Counter      dict subclass for counting hashable objects
* OrderedDict  dict subclass that remembers the order entries were added
* defaultdict  dict subclass that calls a factory function to supply missing values
* UserDict     wrapper around dictionary objects for easier dict subclassing
* UserList     wrapper around list objects for easier list subclassing
* UserString   wrapper around string objects for easier string subclassing
"""

# namedtuple
# 第一次接触namedtuple，是在FluentPython里面
# 就一起利用具名数组来构建一副纸牌吧


# 最简单的demo，创建一个方块7
Card = collections.namedtuple('Card', ['rank', 'suit'])
c = Card("7", "diamonds")
print("方块七的分值:{}， 方块七的花色:{}".format(c.rank, c.suit))


class FrenchDeck:
    """别在意这个名字的含义。。。。"""
    # 生成一个rank的list
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    # 生成一个花色的list， 黑桃，方块，梅花，红心
    suits = 'spades diamonds clubs hearts'.split()

    def __init__(self):
        """初始化我们的纸牌"""
        Card = collections.namedtuple('Card', ['rank', 'suit'])
        self._cards = [Card(rank, suit) for suit in self.suits
                       for rank in self.ranks]

    def __len__(self):
        """返回纸牌的数量"""
        return len(self._cards)

    def __getitem__(self, position):
        """给出位置，返回纸牌
        example:
            c = FrenchDeck()
            print(c[51])
        """
        return self._cards[position]

    def pick(self):
        """选随机牌"""
        return choice(self)

    # 使用staticmethod装饰器，允许在未初始化时直接调用该方法
    # 也就是说，使用FrenchDeck.spades_high(card),和使用普通函数相同
    # example:
    #     c = FrenchDeck().pick()
    #     print(FrenchDeck.spades_high(c))
    @staticmethod
    def spades_high(card):
        """返回牌的分值"""
        suit_values = dict(spades=3, hearts=2, diamonds=1, clubs=0)
        rank_value = FrenchDeck.ranks.index(card.rank)
        return rank_value * len(suit_values) + suit_values[card.suit]


if __name__ == "__main__":
    c = FrenchDeck().pick()
    mark = FrenchDeck.spades_high(c)
    print("随机抽牌为：{}，{}， 分值为：{}".format(c.suit, c.rank, mark))
    # 限3.6 frmot字符串
    print(f"随机抽牌为：{c.suit}，{c.rank}， 分值为：{mark}")

```