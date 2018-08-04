---
layout: post
title:  "Beginning with Phphunit and Drupal 7!"
date:   2018-07-18 16:57:51
---

I know
*Drupal 8* is the current version to develop on Drupal, but nowadays still exist a lot of companies with sites developed on Drupal 7.


So, if you still working on Drupal 7 projects, probably this post will be useful for you, I'm going to show you a little example from a custom module that I create, where a use *Phphunit* to test:

Requirements:

- composer
- Phpunit >= 4.8
- PHP >= 5.6
- Drupal 7

Install Phpunit using composer, remember phpunit 4 is not longer supported.  (https://phpunit.de/getting-started/phpunit-5.html)

{% highlight html %}
composer require --dev phpunit/phpunit ^4.8

{% endhighlight %}



I'm using POO in this custom module, you can download or fork this module here, please note that you need a couple dependencies to install the module:
https://github.com/leydisContre7777/forum_notification


Ok, let's start this example, where I mocked some databases Drupal function's, this is just a small  beginning to test with Phpunit and Drupal 7.


So, In the custom forum_notification module, I created the following class which I call from .module file:

{% highlight html  %}

forum_notification/src/Forum/ForumCounter.php

{% endhighlight %}


This file contains the functions that I'm using to count and save the participation on the Forum configured by the user, then, I created a test:


{% highlight html %}
namespace Drupal\forum_notification\Forum;

/**
 * Created by PhpStorm.
 * User: ldcontreras
 * Date: 30/05/18
 * Time: 18:26
 */
class ForumCounter implements ForumInterface {

	/**
	 * @param $forum
	 * @param $uid
	 * @return array()
	 */
	public function setForumCounter($forum, $uid) {
		$counterState = db_update('forum_counter_states')
			->fields(array(
				'state' => 'read',
			))
			->condition('uid', $uid)
			->condition('tid', $forum)
			->execute();

		return $counterState;

	}

}

{% endhighlight %}




The test of *setForumCounter* will looks like this:
{% highlight html %}
<?php
/**
 * Created by PhpStorm.
 * User: ldcontreras
 * Date: 8/06/18
 * Time: 10:02
 */

namespace Drupal\forum_innovation\Tests;


/**
 * Test the  ForumCounter Class.
 * @coversDefaultClass  \Drupal\forum_innovation\Forum
 * @group utility
 */
class ForumCounterTest extends \PHPUnit_Framework_TestCase {

	/**
	 * @covers::setForumCounter
	 */

	public function testSetForumCounter() {
		$set_counter = $this->getMockBuilder('Drupal\forum_innovation\Forum\ForumCounter')
			->setMethods(array('setForumCounter'))
			->getMock();

		$set_counter->expects($this->once())
			->method('setForumCounter')->will($this->returnValue(TRUE));

		$set_counter->setForumCounter(83, 3024);
	}
}
{% endhighlight %}

The previous test should take it as an example, because the *setForumCounter* only contains the db_update drupal function, and it doesn't make too much sense to test that function, because
unit test shouldn't depends on the database, I mean you can call the drupal bootsrap in a Phpunit Test, but its performace will be very slow and that's why I mocked the database functions. But let's getting  this more sense, creating another function that depends from the *setForumCounter*


{% highlight html %}

function getForumCounter($forum, $uid) {

		$notification = $this->setForumCounter($forum, $uid);

		if ($notification) {  //the notification was read by user

			return ('The notification was read');

		}


}


{% endhighlight %}


The test for getForumCounter function, will looks like this:


{% highlight html %}

public function testGetForumCounter() {
    $set_counter = $this->getMockBuilder('Drupal\forum_innovation\Forum\ForumCounter')
        ->setMethods(array('setForumCounter'))
        ->getMock();

    $set_counter->expects($this->once())
        ->method('setForumCounter')->will($this->returnValue(TRUE));

    $set_counter->setForumCounter(83, 3024);

    $this->assertEquals(
        'The notification was read',
        'The notification was read'
    );
}


{% endhighlight %}


That's it!! pretty simple example to start testing with Phpunit in Drupal 7!
