---
layout: post
title: Honey, I edited Python's Vars()
---

**Sam:** Hey Honey! How did grocery shopping go?

**Honey:** Gosh! It was super tedious! There was this long line to get in and it took forever to get to my turn.

**Sam:** Did you get toilet paper?

**Honey:** No Sam, unfortunately the store is still out of toilet paper.

**Honey:** So what's up, how's your project going? \*Unpacks groceries\*

**Sam:** Lol! I was hoping you wouldn't ask ... yet.

**Honey:** Why? What's the matter? Did you lose all your uncomitted git changes again?

**Sam:** Well what I've lost right now is my mind, cos this shit is driving me completely nuts. So I hit an error not so long after you stepped out. And I've been stuck here since then.

**Honey:** Wow! I was out for quite a while. About 3 hours?

**Sam:** Yeah, if you came in about 45 minutes ago, you would've caught me having a raging conversation with my <s>computer</s> self. Cos it makes absolutely no sense that I'm getting this error.

**Honey:** Hmm, so what's this error you're getting?

**Sam:** Yeah, so I have this Python object that I'm trying to convert to JSON ...

**Honey:** Ok?

**Sam:** So it's an instance of a class named `Dealership` right? So to convert it to JSON, I need to convert the object into a Dictionary right?

**Honey:** Yep! You could do that with `vars()` or the `__dict__` attribute.

**Sam:** Exactly! So I used the `vars()` function to convert the object into a Dictionary right?

**Honey:** Ok?

**Sam:** But the thing is, one of the fields of the object is an object as well.

**Honey:** Ah I see! So you have to convert that field into a Dictionary as well eh?

**Sam:** Yeah, so I created an `as_dict()` function that would help me convert the `dealership` object and its fields into a Dictionary.

**Honey:** Hmm, let me have a look at the object in question. \*Walks down to Sam's Computer\*

```python
class Car:
  def __init__(self, brand: str, condition: bool, price: str):
    self.price = price
    self.brand = brand
    self.condition = condition

# class that needs to be converted to JSON
class Dealership:
  def __init__(self, stores: List[str], car: Car):
    self.car = car
    self.stores = stores
```

**Honey:** Yeah, so the field `car` is alson an object and needs to be converted. Yeah, I get it now. \*Heads back to unpacking groceries\*

**Honey:** So what's the problem with converting that object and its fields into a dictionary?

**Sam:** Well I'm not sure, but everything just seems to go to shit when I run the code.

**Sam:** Like for some reason I cannot access the `car` field anymore, and my server just crashes.

**Honey:** What do you mean you can't access it? Like you don't have read permissions? Did you try with `sudo`?

**Sam:** Haha! No, I mean when I try to access the `dealership.car.price` field I get this weird `AttributeError: 'dict' object has no attribute 'price'` error.

**Sam:** And this is despite not modifying the `dealership` nor the `car` objects. Absolutely weird!

**Honey:** Hmm, may I have a look at what your `as_dict()` function looks like? \*Walks down to Sam's Computer again\*

```python
def as_dict(dealership: Dealership):
  dealership_dict = vars(dealership)
  dealership_dict['car'] = vars(dealership.car)
  return dealership_dict
```

**Honey:** \*Lol\*

**Sam:** What? What's wrong with it?

**Honey:** Welp ... so, the thing is, you shouldn't ever modify the dictionary returned by `vars()`

**Sam:** Why not? Doesn't it return a new Dictionary all the time?

**Honey:** err... no it doesn't. It returns the `__dict__` value of that object. So in your case, it returns `dealership.__dict__` ...

**Sam:** \*confused\*

**Honey:** ... And if you modify that, you are essesntially modifying that object. Because the `__dict__` value is a reference to the fields of that object.

**Sam:** \*confused\*

**Sam:** So you're saying the line `dealership_dict['car'] = vars(dealership.car)` is the problem?

**Honey:** Yep! Try printing the `type` of `dealership.car` in the console after the `as_dict()`  function was called

**Sam:** Like this?

```python
def as_dict(dealership: Dealership):
  dealership_dict = vars(dealership)
  dealership_dict['car'] = vars(dealership.car)
  
  # printing the `type` of `dealership.car` in the console
  print(type(dealership.car))
  return dealership_dict
```

**Honey:** Yeah, lets run that and see what gives.

**Console:** `<class 'dict'>`

**Honey:** Yeah, so you've by accident, permanently converted `dealership.car` field into a dictionary.

**Sam:** Ah darn! How then do I convert the object and its fields to a dictionary without modifying the object itself?

**Honey:** Well, You could create a copy of the dictionary returned by `vars()` and modify that.

**Sam:** Ah! Like this?

```python
def as_dict(dealership: Dealership):
  # create a copy of the dictionary returned by `vars()`
  dealership_dict = {key: value for key, value in vars(dealership).items()}
  dealership_dict['car'] = vars(dealership.car)
  return dealership_dict
```

**Honey:** Yep! That should work fine now.

**Sam:** \*Phew\* I can't believe this cost me over 3 hours of my time. Thanks Honey!

**Honey:** Yeah well, now you know what the issue is, so it won't be costing you anymore hours in the future. \*wink\*


*Did you enjoy reading this? Is something off about it? Please let me know in the comments*

