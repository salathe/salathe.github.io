---
layout: post
title: PHP5 Method Chaining
excerpt: If you <code>return $this</code> then you can chain method calls together.
---
<strong>Introduction</strong>

In this article, I'll be talking about a useful new feature introduced in PHP5 as part of the OOP improvements over PHP4.  This feature is called Method Chaining and enables us to do pretty cool things like:

<pre lang="php">
$object->method_a()->method_b()->method_c();
</pre><a id="more"></a><a id="more-15"></a>

So really what's this all about?  Well, one change (of many) between PHP4 and PHP5 is that now methods can return <code>objects</code>.  That's only a small change but allows for a new way of thinking about how you handle objects and their methods.

In order to be able to chain methods together, as mentioned earlier, you need to be able to return an object.  The returned object does not necessarily need be the same one from which the method is being called, but we'll not be touching on that fact in this article.  Let's go for a quick illustration of how to put all of this into action.

<strong>Regular Use of a Class</strong>
We'll start off with a basic class, which doesn't do anything particularly interesting but it will serve our purpose.

<pre lang="php">
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
</pre>

Now, we can create a new <code>Person</code> (see example below) and give them a name and age.  Using the <code>introduce</code> method, the person can them introduce themselves.

<pre lang="php">
// We'll be creating me, digitally.
$peter = new Person();

// Let's set some attributes and let me introduce myself.
$peter->setName('Peter');
$peter->setAge(23);
$peter->introduce();
</pre>

The above example would output:
<blockquote><code>Hello my name is Peter and I am 23 years old.</code></blockquote>


<strong>Implementing Method Chaining</strong>
Great, but you already knew how to do that didn't you!  In actuality, we only need to add two lines of code to the basic class above to enable us to write a chain, and the second line is a repeat of the first!  How's it done?  Quite simply we need to return the <code>Person</code> object from each method.  At the moment, none of the methods actually return anything so we'll amend  that now.  We just need to add <code>return $this;</code> to the methods <code>setName</code> and <code>setAge</code>.

The amended <code>Person</code> class is as follows:
<pre lang="php">
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
</pre>

Ok, is that it? Yep, really.  Because we return the <code>Person</code> from those methods, it enables us to group method calls together in a sequence, or chain.  I'll re-do the previous example but this time using our new magical chaining abilities.

<pre lang="php">
// We'll be creating me, digitally.
$peter = new Person();

// Let's set some attributes and let me introduce myself, 
// all in one line of code.
$peter->setName('Peter')->setAge(23)->introduce();
</pre>

That example will produce exactly the same output as our regular, boring example earlier! All because we return the <code>Person</code> object in the <code>setName/Age</code> methods.

<strong>How does that work?</strong>
If you're a bit confused about precisely what is going on here, let me try to walk you through the process, step by step. We'll go through the line of code as it is processed, from left to right.

First up is <code>$peter->setName('Peter')</code>.  This assigns the person's name to be Peter, my name and returns <code>$this</code> -- that is, the <code>$peter</code> object.  So at the moment Peter has his name, but no age!  

Next up comes <code>->setAge(23)</code>.  Because we're chained with the previous method, PHP interprets the code and says "execute the <code>setAge</code> method belonging to whatever was returned from the previous method."  In this case, PHP executes the <code>setAge</code> method belonging to our <code>Person</code> object, <code>$peter</code>.

The exact same thing happens when we finally call the <code>introduce</code> method.  PHP executes the <code>introduce</code> method belonging to whatever was returned from the <code>setAge</code> method call: <code>$peter</code>.

Hopefully, that's at least a bit clear and I'll let you mull over this confusing (until you <em>get</em> it) subject for a while.  I'll leave you with the note that because we can chain methods, they can be called more than once and in whatever order we like which could lead to the amazingly useless snippet below:
<pre lang="php">
// Hello my name is Winifred and I am 72 years old.
$peter->setAge(23)
      ->setName('Peter')
      ->setName('Winifred')
      ->setAge(72)
      ->introduce();
</pre>

<strong>In Conclusion</strong>
We can boil this whole article down into one short sentence.  If you <code>return $this</code> then you can chain the method calls together. Done. :)
