---
layout: post
title: PHP5 Method Chaining
excerpt: If you <code>return $this</code> then you can chain method calls together.
---

## Introduction

In this article, I'll be talking about a useful new feature introduced in
PHP5 as part of the OOP improvements over PHP4.  This feature is called
"method chaining" and enables us to do pretty cool things like:

{% highlight php startinline %}
$object->method_a()->method_b()->method_c();
{% endhighlight %}

So really what's this all about?  Well, one change (of many) between PHP4 and
PHP5 is that now methods can return objects.  That's only a small change but
allows for a new way of thinking about how you handle objects and their methods.

In order to be able to chain methods together, as mentioned earlier, you need
to be able to return an object.  The returned object does not necessarily
need be the same one from which the method is being called, but we'll not
be touching on that fact in this article.  Let's go for a quick illustration
of how to put all of this into action.

## Regular use of a class

We'll start off with a basic class, which doesn't do anything particularly
interesting but it will serve our purpose.

{% highlight php startinline %}
// This Person class encapsulates a couple of properties which
// a person might have: their name and age.
// We also give the Person the opportunity to introduce themselves.
class Person
{
	private $m_szName;
	private $m_iAge;
	
	public function setName($szName)
	{
		$this->m_szName = $szName;
	}
	
	public function setAge($iAge)
	{
		$this->m_iAge = $iAge;
	}
	
	public function introduce()
	{
		printf(
			'Hello my name is %s and I am %d years old.',
			$this->m_szName,
			$this->m_iAge);
	}
}
{% endhighlight %}

Now, we can create a new `Person` (see example below) and give them a name and
age.  Using the `introduce()` method, the person can them introduce themselves.

{% highlight php startinline %}
// We'll be creating me, digitally.
$peter = new Person();

// Let's set some attributes and let me introduce myself.
$peter->setName('Peter');
$peter->setAge(23);
$peter->introduce();
{% endhighlight %}

The above example would output:

> Hello my name is Peter and I am 23 years old.

## Implementing Method Chaining

Great, but you already knew how to do that didn't you!  In actuality, we only
need to add two lines of code to the basic class above to enable us to write a
chain, and the second line is a repeat of the first!  How's it done?  Quite
simply we need to return the `Person` object from each method.  At the moment,
none of the methods actually return anything so we'll amend  that now.  We just
need to add `return $this;` to the methods `setName()` and `setAge()`.

The amended `Person` class is as follows:

{% highlight php startinline %}
// This Person class encapsulates a couple of properties which
// a person might have: their name and age.
// We also give the Person the opportunity to introduce themselves.
class Person
{
	private $m_szName;
	private $m_iAge;
	
	public function setName($szName)
	{
		$this->m_szName = $szName;
		return $this; // We now return $this (the Person)
	}
	
	public function setAge($iAge)
	{
		$this->m_iAge = $iAge;
		return $this; // Again, return our Person
	}
	
	public function introduce()
	{
		printf(
			'Hello my name is %s and I am %d years old.',
			$this->m_szName,
			$this->m_iAge);
	}
}
{% endhighlight %}

Ok, is that it? Yep, really.  Because we return the `Person` from those methods,
it enables us to group method calls together in a sequence, or chain.  I'll
re-do the previous example but this time using our new magical
chaining abilities.

{% highlight php startinline %}
// We'll be creating me, digitally.
$peter = new Person();

// Let's set some attributes and let me introduce myself, 
// all in one line of code.
$peter->setName('Peter')->setAge(23)->introduce();
{% endhighlight %}

That example will produce exactly the same output as our regular, boring example
earlier! All because we return the `Person` object in the `setName/Age()` methods.

## How does that work?

If you're a bit confused about precisely what is going on here, let me try to
walk you through the process, step by step. We'll go through the line of code
as it is processed, from left to right.

First up is `$peter->setName('Peter')`.  This assigns the person's name to be
Peter, my name and returns `$this` -- that is, the `$peter` object.
So at the moment Peter has his name, but no age!

Next up comes `->setAge(23)`.  Because we're chained with the previous method,
PHP interprets the code and says "execute the `setAge()` method belonging to
whatever was returned from the previous method."  In this case, PHP executes
the `setAge` method belonging to our `Person` object, `$peter`.

The exact same thing happens when we finally call the `introduce()` method.
PHP executes the `introduce()` method belonging to whatever was returned from
the `setAge()` method call: `$peter`.

Hopefully, that's at least a bit clear and I'll let you mull over this
confusing (until you _get_ it) subject for a while.  I'll leave you with the
note that because we can chain methods, they can be called more than once and
in whatever order we like which could lead to the amazingly useless snippet below:

{% highlight php startinline %}
// Hello my name is Winifred and I am 72 years old.
$peter->setAge(23)
      ->setName('Peter')
      ->setName('Winifred')
      ->setAge(72)
      ->introduce();
{% endhighlight %}

## In Conclusion

We can boil this whole article down into one short sentence.
If you `return $this` then you can chain the method calls together.
Done. :)
