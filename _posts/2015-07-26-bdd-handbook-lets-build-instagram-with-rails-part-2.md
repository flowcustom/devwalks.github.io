---
title: "BDD Handbook - Let's Build Instagram (with Rails) - Part 2"
layout: post
permalink: /bdd-handbook-lets-build-instagram-with-rails-part-2/
published: 26 July 2015
category: tech instgram
feature_image: /content/images/2015/07/Screen-Shot-2015-07-16-at-11-23-20-pm.png
description: Test driven user comments and authentication for our Instagram Ruby on Rails project
id: 6
---
<sub class='blog-date'>{{ page.published }}</sub>

## You want to learn to test

Of course you do!  Not only does literally every Rails job require it as a prerequisite, it's also a great way of building your web apps.

In this article, we’re going to look at the tests that could be written in order to BDD our way through the features we built in the [non-tested version of part 2](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/).    These features were:

- Users and user authentication (along with non-standard devise columns, user names in this instance).
- Protecting parts of our web application depending on user authentication and user ownership.
- Commenting on posts as a logged in user.

Want a quick-start?  

Clone my Photogram repo from [here](https://github.com/benwalks/photogram_TDD/commit/b763f9f4ba46a73f13c33dd24ab659d66294ab2b), the end of BDD Part 1.

What we’re *not* going to do in this article is re-do everything in [the non-tested version of Let's Build Instagram - part 2](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/).

What you're going to need to do instead is to simply open up the [non-tested version of the guide](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/) as a reference whenever your tests give you an error that you’re not sure how to solve.  

I will, of course, give you a few hints as to what the process will look like too. 

***I’m not a complete monster.***

As per the [last test driven version](http://www.devwalks.com/lets-build-instagram-test-driven-with-ruby-on-rails-part-1/), I’m going to write up a pseudo code version of the tests too, just so you know what we’re trying to achieve.  I highly recommend you try to translate that pseudo code into actual Rspec / capybara code yourself before you take a peek at my version.

Let’s roll!

## Features Tests for Creation and Authentication of Users via Devise

First things first, in [Part 2](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/) we wanted to allow our members to have the ability to create an account and then log in and out of Photogram.  Let’s write the test for creating an account first.  Below is a pseudo-code version.

```ruby
feature 'Creating a new user' do
  background do
    # Visit the home page
    # Click the 'Register' link
  end
  scenario 'can create a new user via the index page' do
    # Fill in the 'User name' field with ’sxyrailsdev'
    # Fill in the 'Email' field with 'sxyrailsdev@myspace.com'
    # Fill in the 'Password' field with 'supersecret'
    # Fill in the 'Password confirmation' field with 'supersecret'
    # Click the 'Sign up' button
    # Expect the page to have the message 'Welcome! You have signed up successfully.'
   end
end
```

Make sense?  Try to translate that pseudo-code for yourself now.  Once happy, take a peek at my version below and then feel free to start running your tests!  

Below my test code below, I’ll quickly run through the steps you can expect to take in the BDD process.

![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

My spec:

```ruby
require 'rails_helper'

feature 'Creating a new user' do
  background do
    visit '/'
    click_link 'Register'
  end
  scenario 'can create a new user via the index page' do
    fill_in 'User name', with: 'sxyrailsdev'
    fill_in 'Email', with: 'sxyrailsdev@myspace.com'
    fill_in 'Password', with: 'supersecret', match: :first
    fill_in 'Password confirmation', with: 'supersecret'

    click_button 'Sign up'
    expect(page).to have_content('Welcome! You have signed up successfully.')
  end
end
```

___

Now that you have your test written, run rspec in your terminal to see what steps you need to take to implement our new feature.  

Want some guidelines on how to flesh out this feature?  Checkout my steps below.  Remember, when in doubt on how to actually implement some functionality, refer back to the [Part 2 non-tested version](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/).

###RSPEC TIME!

*Expected Steps through your Rspec error*

- You’re going to need to create a Register button in your navbar.
- Rspec is going to complain you’ve got no fields to fill in.  You’re going to need to install the Devise gem in your project and follow their installation instructions [as per their docs](https://github.com/plataformatec/devise).  Call your Devise model: User.
- You’re going to have to actually link your Register link to the Devise link for creating new users.  Not sure what that is?  Run ```bin/rake routes``` in your terminal to see your current available paths.
- Capybara *still* won’t be able to find the User name field!  Why I wonder?  Probably because Devise doesn’t include it as a default!  Refer to my non-tested [Part 2](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/) article on how to implement the user name feature.

And that should be it!

Let’s add some more tests to that same feature spec.  In these, we’ll cover some scenarios that we want to fail.  Implement each separately and only continue once you’ve got a passing test.

#### We DEMAND a user name for our users

Let’s write a test to make sure our users MUST include a user name when they create their account.  This test is *very similar* to the above, but we just skip the step where we fill in the field for 'User name'  Remember, each new test below that’s related to our 'creating new users' spec will be kept in the same spec file, just add the additional code below the existing.

```ruby
  scenario 'requires a user name to successfully create an account' do
    # Fill in the 'Email' field with 'sxyrailsdev@myspace.com'
    # Fill in the 'Password' field with 'supersecret'
    # Fill in the 'Password confirmation' field with 'supersecret'
    # Click the 'Sign up' button
    # Expect the page to have the message 'You need a user name to create an account.'
```

Implement it yourself, it’s very similar to our first test above!

![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

```ruby
  scenario 'requires a user name to successfully create an account' do
    fill_in 'Email', with: 'sxyrailsdev@myspace.com'
    fill_in 'Password', with: 'supersecret', match: :first
    fill_in 'Password confirmation', with: 'supersecret'

    click_button 'Sign up'
    expect(page).to have_content("can't be blank")
  end
```

___

###RSPEC TIME!

*What you can expect...*

- A simple User model validation rule should be enough to solve this error.

#### Not too short, not too long, just right

Great!  So our users can create an account with a user name and we’re making sure our users *need* to have a user name.  Next, let’s make sure our users can’t call themselves anything too crazy.  If we have people calling themselves 'a', 'z' or 'map', anarchy will prevail.

Let’s make sure our users can only use user names that are longer than three characters.

Pseudo-code test:

```ruby
  scenario 'requires a user name to be more than 4 characters' do
    # Fill in the 'User name' field with 'h'
    # Fill in the 'Email' field with 'sxyrailsdev@myspace.com'
    # Fill in the 'Password' field with 'supersecret'
    # Fill in the 'Password confirmation' field with 'supersecret'
    # Click the 'Sign up' button
    # Expect the page to have the message 'minimum is 4 characters'
```

Guess what?  It’s your turn!

![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

My Rspec code:

```ruby
  scenario 'requires a user name to be more than 4 characters' do
    fill_in 'User name', with: 'h'
    fill_in 'Email', with: 'sxyrailsdev@myspace.com'
    fill_in 'Password', with: 'supersecret', match: :first
    fill_in 'Password confirmation', with: 'supersecret'

    click_button 'Sign up'
    expect(page).to have_content('minimum is 4 characters')
  end
```

___

###RSPEC TIME!

*What can you expect from these errors?*

- Our application currently has no rules for specific user name lengths, maybe you can add that to your user\_name validation in the User.rb model?
- Devise should take care of the rest

What about ridiculously long user names?  That would be super annoying.  Let’s lay down some rules and say that an arbitrary 12 characters is the maximum length we’ll accept for our user names.

Implement this yourself now (no pseudo code provided, you’re great at this now).

![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

Here’s my implementation.

```ruby
  scenario 'requires a user name to be less than 12 characters' do
    fill_in 'User name', with: 'h' * 13
    fill_in 'Email', with: 'sxyrailsdev@myspace.com'
    fill_in 'Password', with: 'supersecret', match: :first
    fill_in 'Password confirmation', with: 'supersecret'

    click_button 'Sign up'
    expect(page).to have_content("maximum is 12 characters")
  end
```

___

###RSPEC TIME!

*Hows about these errors?*

- At first, your account will be created.  Just what we didn’t want. Create another rule within your User.rb model user\_name validation.  Add a maximum length.

**AND THAT’S IT!**

Your first feature spec for this part is complete!  Now, let’s quickly make sure our users can log in and out.

#### Logging in. Logging out. Factory Creation.

While logging in and out of an application is core Devise functionality, let’s quickly make sure it’s working in our application.  Our tests will be simple, but, we will need to create a User factory with factory\_girl to simplify the testing process.

Let’s create the factory now.  No, wait.  Do you remember how to create a default factory?  We did it in Part 1 for our posts.  Give it a go now, checkout the factory\_girl docs and try to implement it for yourself.

![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

Devise should’ve been nice enough to create a User factory for you under the ```spec/factories``` folder. If not, create a new file called ```users.rb``` now within that same folder.  Your factory should looks something like the below, feel free to customise the fields though, I assure you 'Arnie' isn’t compulsory.

```ruby
FactoryGirl.define do
  factory :user do
    email 'fancyfrank@gmail.com'
    user_name 'Arnie'
    password 'illbeback'
    id 1
  end
end
```

___

Brilliant!  What does this mean?  Well, it means we don’t have to manually create a user every single test by clicking around the place and entering data into fields.  We can just throw our ```user = create :user``` code in a background block in our test and we then have a user to work with! We’ll still have to login though.

#### Logging in as Arnie

Alright, time to ensure we can log in ok as our user.  Create a new spec file within the features folder and call it ```user_authentication_spec.rb```

Here’s some pseudo code for the authentication spec.  What we want from this feature is to be able to log in and see a flash message.  We also want the 'Login' link in the navbar to change to 'Logout' and we also want the 'Register' link to be replaced with the 'New Post' link. 

```ruby
require 'rails_helper'

feature 'User authentication' do
  background do
    # create our user factory
  end
  scenario 'can log in from the index via dynamic navbar' do
    # visit the index
    # expect the page to not have the 'New Post' link yet
    
    # click the 'Login' link
    # fill in the email field with the user’s email
    # fill in the password field with the user’s password
    # click the 'Log in' button
    
    # expect the page to have content saying 'Signed in successfully.'
    # expect the 'Register' link to disappear
    # expect the 'Logout' link to be present
  end
end
```

Not too hard, right?  Your turn now, turn this into a beautiful feature spec!

![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

```ruby
require 'rails_helper'

feature 'User authentication' do
  background do
    user = create(:user)
  end
  scenario 'can log in from the index' do
    visit '/'
    expect(page).to_not have_content('New Post')
    
    click_link 'Login'
    fill_in 'Email', with: 'fancyfellows@gmail.com'
    fill_in 'Password', with: 'illbeback'
    click_button 'Log in'

    expect(page).to have_content('Signed in successfully.')
    expect(page).to_not have_content('Register')
    expect(page).to have_content('Logout')
  end
end
```

___

###RSPEC TIME! 
(Only run the specific spec by running ```rspec spec/features/user_authentication_spec.rb``` in your terminal.)

*What can you expect?*

- The 'New Post' button is present, even though our user hasn’t logged in yet!  Outrageous!  Write some login in your view so that only the 'Register' and 'Login' links are available when there’s no logged in user.  Refer to the [non-tested](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/) version of this guide for reference.
- We won’t be able to find the email field unless you added links during the last error.  Why?  Is it because our navbar 'Login' link doesn’t actually link to our Devise login route?  Make sure the link is pointing to the right route.

Great!  Now, let’s make sure we can simply log out.  Add the next spec below your spec above, still within the ```user_authentication_spec.rb```.

Here’s what we want from this feature:

```ruby
scenario 'can log out once logged in' do
  # visit the index
  # click the 'Login' link
  # fill in the email field with your user’s email
  # fill in the password field with your user’s password
  # click the 'Log in' button
  # click the 'Logout' link
  # expect to see the text 'Signed out successfully.'
end
```

Convert this now.

![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

```ruby
  scenario 'can log out once logged in' do
    visit '/'
    click_link 'Login'
    fill_in 'Email', with: 'fancyfellows@gmail.com'
    fill_in 'Password', with: 'illbeback'
    click_button 'Log in'

    click_link 'Logout'
    expect(page).to have_content('Signed out successfully.')
  end
```

___

### RSPEC TIME!

*What can you expect from this round of testing and building?*

- It depends!  Did you implement all of the links from the non-tested version of this guide?  If not, Rspec will be upset that you’re not receiving the message you want.  Make sure your 'Logout' link is working as per the Devise routes for such things.  
Remember, you can check your routes by running ```bin/rake db:routes``` in your terminal.  You want the destroy user session path.

Good stuff!  We can log in, log out but we’re not actually protecting anything thus far are we?  If we’re logged out, we can still see all of the posts on the index!  We can also create new posts like some sort of anarchist...

This just won’t do.

#### Logged in?  You can do stuff!  Logged out?  No way Jose!

Let’s set some permissions as a part of this authentication spec.  First, we don’t want non-logged in users to be able to see posts, create posts, edit posts or delete posts.  We only want logged in users to be able to see and do such things.  Let’s test our way to that functionality now.

Let’s think about this in pseudo code first, how can we prove that un-authenticated user doesn’t have access?  We’ll focus on blocking un-authorised users from viewing posts on the index and creating new posts in the below specs.

```ruby
scenario 'cannot view index posts without logging in' do
  # visit the root route
  # expect the page to have content saying ’You need to sign in or sign up before continuing.’
end

scenario ' cannot create a new post without logging in' do
  # visit the new_post_path
  # expect the page to have content saying 'You need to sign in or sign up before continuing.'
end
```

Try to translate this pseudo-code into our spec now.

![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

```ruby
  scenario 'cannot view index posts without logging in' do
    visit '/'
    expect(page).to have_content('You need to sign in or sign up before continuing.')
  end
  
  scenario 'cannot create a new post without logging in' do
    visit new_post_path
    expect(page).to have_content('You need to sign in or sign up before continuing.')
  end
```

___

### RSPEC TIME! 

*What can you expect to see and implement when running these specs?*

- For both scenario’s you’re not going to see our error message.  Why?  Because anyone can access anything at the moment.  If only Devise had a before\_action helper for our posts controller that would restrict access to our actions... (refer to the [non-tested](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/) article if you need extra hints).

Wait a second, both of our new tests are passing once we add the Devise method to our posts\_controller as a before action but... our 'logging out' scenario doesn’t work anymore!

Why?

Look at the flow of the test.  Jump into your browser and do the steps for yourself to build some understanding.

What’s happening is that when you’re logged out, Devise tries to redirect you back to a route that has now been blocked.  What does this mean?  We get a different flash message, one that tells us we need to log in, not that we’ve successfully logged out.

Adjust the scenario so it’ll pass with the new flash message.  It’ll now look like this:

```ruby
  scenario 'can log out once logged in' do
    visit '/'
    click_link 'Login'
    fill_in 'Email', with: 'fancyfellows@gmail.com'
    fill_in 'Password', with: 'illbeback'
    click_button 'Log in'

    click_link 'Logout'
    expect(page).to have_content('You need to sign in or sign up before continuing.')
  end
```

Re-run Rspec for your ```user_authentication_spec.rb``` and you should only see green passing test.  Nice work.

## Revisiting Our Whole Feature Test Suite

So we’ve been running only the one spec file for a little while now, let’s run the whole suite again by not specifically defining a test to run. On my dev machine, the command is simply ```rspec``` in the terminal.

**Sweet Social Media Gods - 7 Failing Specs!**

This is outrageous!  What in DHH’s name is going on?  Let’s look at the actual errors...

```ruby
  1) Creating posts can create a new post
     Failure/Error: click_link 'New Post'
     Capybara::ElementNotFound:
       Unable to find link "New Post"
```

Oh yeah, we’ve implemented users now!  We’ve blocked off all access to our links and even our index page *unless* you’re logged in!  

**We’re going to have to adjust our older specs.**

Here’s what I want you to do.  Go back to the failing specs and think about how you can add a background block prior to the tests where you instantiate your user factory and then login with that user.  By having these actions in a before block, we’ll keep it nice and DRY and not have to type those actions over and over for each test that needs access.

Go on, implement it yourself now!  You’ll have to adjust 5 older specs but it’s not as painful as it seems, I assure you.

![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

Here’s my updated ```creating_posts_spec.rb```.

```ruby
require 'rails_helper'

feature 'Creating posts' do
  background do
    user = create :user
    visit '/'
    fill_in 'Email', with: user.email
    fill_in 'Password', with: user.password
    click_button 'Log in'
  end
  scenario 'can create a new post' do
    visit '/'
    click_link 'New Post'
    attach_file('Image', "spec/files/images/coffee.jpg")
    fill_in 'Caption', with: "nom nom nom #coffeetime"
    click_button 'Create Post'
    expect(page).to have_content("#coffeetime")
    expect(page).to have_css("img[src*='coffee']")
  end
  scenario 'a post needs an image to save' do
    visit '/'
    click_link 'New Post'
    fill_in 'Caption', with: "No picture because YOLO"
    click_button 'Create Post'
    expect(page).to have_content("Halt, you fiend! You need an image to post here!")
  end
end
```

And my updated ```deleting_posts_spec.rb```:

```ruby
require 'rails_helper'

feature 'deleting posts' do
  background do
    post = create(:post, caption: 'Abs for days.')
    user = create :user

    visit '/'
    fill_in 'Email', with: user.email
    fill_in 'Password', with: user.password
    click_button 'Log in'

    find(:xpath, "//a[contains(@href,'posts/1')]").click
    click_link 'Edit Post'
  end
  scenario 'can delete a post' do
    click_link 'Delete Post'

    expect(page).to have_content('Problem solved!  Post deleted.')
    expect(page).to_not have_content('Abs for days.')
  end
end
```

My updated ```displaying_index_posts_spec.rb```:

```ruby
require 'rails_helper'

feature 'Can see a list of posts on the index' do
  background do
    post_one = create(:post, caption: "This is post one")
    post_two = create(:post, caption: "This is the second post")
    user = create :user

    visit '/'
    fill_in 'Email', with: user.email
    fill_in 'Password', with: user.password
    click_button 'Log in'
  end
  scenario 'the index lists all posts' do
    expect(page).to have_content("This is post one")
    expect(page).to have_content("This is the second post")
    expect(page).to have_css("img[src*='coffee']")
  end
end
```

The new ```editing_posts_spec.rb```:
```ruby
require 'rails_helper'

feature 'editing posts' do
  background do
    post = create :post
    user = create :user

    visit '/'
    fill_in 'Email', with: user.email
    fill_in 'Password', with: user.password
    click_button 'Log in'
    
    find(:xpath, "//a[contains(@href,'posts/1')]").click
    click_link 'Edit Post'
  end
  scenario 'can edit a post' do
    fill_in 'Caption', with: "Oh god, you weren't meant to see this picture!"
    click_button 'Update Post'

    expect(page).to have_content("Post updated hombre")
    expect(page).to have_content("Oh god, you weren't meant to see this picture!")
  end
  scenario "a post won't update without an attached image" do
    attach_file('Image', 'spec/files/coffee.zip')
    click_button 'Update Post'

    expect(page).to have_content("Something is wrong with your form!")
  end
end
```

And last, but not least, the ```viewing_posts_spec.rb```

```ruby
require 'spec_helper'

feature 'viewing individual posts' do
  background do
    user = create :user
    post = create :post

    visit '/'
    fill_in 'Email', with: user.email
    fill_in 'Password', with: user.password
    click_button 'Log in'
  end
  scenario 'can click and view a single post from the index' do
    find(:xpath, "//a[contains(@href,'posts/1')]").click
    expect(page.current_path).to eq(post_path(1))
  end
end
```

___

Wait a second, we’re logging in so much during our tests, surely we can clear this up a little?  

Yes, yes we can.  

Let’s create a helper method, within a module, within our ```rails_helper.rb``` file.  Open it up and add this code *above* the Rspec configure block.

```ruby
module AuthHelpers
  def sign_in_with (user)
    visit '/'
    fill_in 'Email', with: user.email
    fill_in 'Password', with: user.password
    click_button 'Log in'
  end
end
```

Now, you’ll have to make sure this helper method is available in our specs by including it within the Rspec configure block we were avoiding before.  Here’s how we include it:

```ruby
config.include AuthHelpers, type: :feature
```

All done!  Now, you can go back and replace the repeated logging in actions in the large majority of your spec files now.  Simply replace the old code used to log in with:

```ruby
sign_in_with user
```

You’ll have to ensure you’ve already created the user factory before you call our new helper method.

Nice work!  You now have a fully passing test suite with some nice helpers and you should feel as good about yourself as you look (fabulous).  Let’s keep adding features now, let’s keep building an awesome application.

## A post should belong to a user, it’s only natural.

It’s true!  Up until now, posts have been completely anonymous and it’s just not right.  How are we meant to flaunt our extravagant lifestyles if no-one knows it’s us?

Let’s write a test to ensure each post has an associated user.  Even better, let’s simply adjust one of our existing tests to allow for our new functionality.

Within your existing ```creating_new_posts_spec.rb```, add the following to the end of the 'can create a new post' scenario:

```ruby
expect(page).to have_content('Arnie')
```

What does this achieve?  Well, we’re simply looking for the string, 'Arnie'.  After all, we created this post whilst logged in as 'Arnie', so that’s the user name that should be displayed on the resulting post!

Run your new test.

### RSPEC TIME!

And... it doesn’t exist...

Oh wait, of course it doesn’t, we haven’t defined a relationship between user and posts at all!  Not only that, we don’t have any dynamic code within our view that will present the name of the post’s owner!  It’s time for you to flesh out the relationship between posts and users, with the [non-tested](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/) version of this article as a guide.

*What can you expect to do to achieve this feature?*

- Well, we’re going to have to create our new posts via a link between the post model and the user model.  The code ```@post = current_user.posts.build``` feels like it has significance...
- Oh, we have no link between the user and post models, therefore ```current_user.posts``` doesn’t mean anything.  What if we created a new db migration so that posts references users?  We do want a user to have\_many posts and a post to belong\_to a user after all! (don’t forget to mention those facts in the models...)
- Still have failing tests?  Is it because there’s a method missing for nil class?  You’re going to have to add some user\_id’s to your :post factories.  Make sure that you’re instantiating your user factory first, and then when creating the post factory, you can do something like:

```ruby
user = create :user
post = create( :post, user_id = user.id )
```

You might have to do this for a few of your current specs.

Battled through all of the errors in order to create beautiful functionality?  Brilliant!  Let’s finally add some extra protection in our application so that only the owners of posts can edit or delete them.

## Get your hands off my posts!

Trying to play shenanigans with my posts eh?!  We’ll see about that, friend.

I’m going to adjust our existing spec ```editing_posts_spec.rb``` to take advantage of our new users and therefore ownership of specific posts.  We’ll adjust the deleting functionality soon too.
 
Try fleshing this functionality now for the editing of posts BUT don’t expect your test to be exactly the same as mine, because it won’t be.  

We want to adjust our spec so that users who didn’t create a specific post can’t edit it, only the creator can.
 
 Good luck!
 
 ![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

Here’s my new spec!

```ruby
require 'rails_helper'

feature 'editing posts' do
  background do
    user = create :user
    user_two = create(:user, email: 'hi@hi.com',
                             user_name: 'BennyBoy',
                             id: user.id + 1)
    post = create(:post, user_id: user.id)
    post_two = create(:post, user_id: user.id + 1)

    sign_in_with user
    visit '/'
  end

  scenario 'can edit a post as the owner' do
    find(:xpath, "//a[contains(@href,'posts/1')]").click
    expect(page).to have_content('Edit Post')

    click_link 'Edit Post'
    fill_in 'Caption', with: "Oh god, you weren't meant to see this picture!"
    click_button 'Update Post'

    expect(page).to have_content('Post updated hombre')
    expect(page).to have_content("Oh god, you weren't meant to see this picture!")
  end

  scenario "cannot edit a post that doesn't belong to you via the show page" do
    find(:xpath, "//a[contains(@href,'posts/2')]").click
    expect(page).to_not have_content('Edit Post')
  end

  scenario "cannot edit a post that doesn't belong to you via url path" do
    visit "/posts/2/edit"
    expect(page.current_path).to eq root_path
    expect(page).to have_content("That post doesn't belong to you!")
  end

  scenario "a post won't update without an attached image" do
    find(:xpath, "//a[contains(@href,'posts/1')]").click
    click_link 'Edit Post'
    attach_file('Image', 'spec/files/coffee.zip')
    click_button 'Update Post'

    expect(page).to have_content('Something is wrong with your form!')
  end
end
```

___

What have we got here?

- A couple of new factories.  One for a bonus user whose post doesn’t belong to us and the other for the soon to be protected post.
- A revised 'can edit a post as the owner' scenario, where we created a new expectation for the 'Edit Post' link to be available to us.
- A couple of new scenarios that ensure we can’t a) Edit a post via the 'Edit Post' link on the show view and b) Edit a post via the direct edit path.

Great, now we can run Rspec and work our way through the new errors we can expect to find.  Go on, do your thang!

### RSPEC TIME!

*What can you expect from this round of tests?*

- You’re going to be able to see the 'Edit Post' link, even though you’re not the owner!  Add some logic to your view so that the 'Edit Post' link is only shown if the current\_users id is equal to the posts owners id.
- You’re also going to *still* be able to access the edit view for a post by directly navigating to the edit url!  How sneaky.  You’re going to have to block the users whose id doesn’t equal the ```post.user.id``` from that controller action and redirect them to the root path with our special flash message as per our spec.

Remember, we cover all of this over in the [non-tested version](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/), so if you ever need any help, just do a quick search over there.

**Let’s protect our posts from being deleted by strangers now.**

Actually, wait! By blocking access to the edit action within our controller, we also block any unauthorised users from hitting that delete button within our edit view.  

What we *should* do though is block unauthorised using from accessing that delete functionality by being sneaky and sending a DELETE request to a post path.  What’s stopping someone from doing the same with our 'create' action too?  What if someone sent a PATCH request to a non-owned post?

We can block these in the controller, just like we did for our edit action (when it comes time to making this test work).  In fact,it’s the perfect time to introduce a new private method within our controller that we’ll then use within a 'before\_action' for the :edit, :update and :destroy actions.

Here’s what that’ll look like in our controller:

```ruby
# Below our other before_actions
  before_action :owned_post, only: [:edit, :update, :destroy]

# Below our other private methods
  def owned_post
    unless @post.user.id == current_user.id
      flash[:alert] = "That post doesn't belong to you!"
      redirect_to root_path
    end
  end
```

Now you can also delete the ownership logic from your edit action, as it’d simply be duplicating what we’ve created here.

You’ve done super awesome and you should be proud.  Let’s move on to our next big feature build.  The ability for our users to comment on posts and abuse their friends.

## Tell me I’m beautiful

Oh you are, stop being so self-conscious.  Look at your beautiful hair and cheekbones.  Don’t even get me started on that shirt you’re wearing.  

It matches your eyes perfectly.

But enough with the compliments, let’s build out some features so that people can actually tell you how great your shirt looks via comments on your posts.  

In the original [non-tested](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/) version of this article, we build a non-AJAX version of this feature first, and then implemented the AJAX features afterwards.  In this version, we’re going to BDD the non-ajax version and you can later adjust to the more streamlined AJAX version once you’ve finished if you so desire.

Prior to getting too excited with this comment feature build, I first want to tidy up a few things so that we’re in line with the non-tested version of this guide.  Let’s quickly do that now.

#### Post Partial Please

Let’s move the code for each individual post into it’s own partial view.  Why?  This will let us reference that same partial from both the index and show views, keeping our code nice and DRY.

While we’re at it, I’m going to also add some styling touches to the post view so that we can pretty it up in the near future with some CSS.

Create a new file under your ```views/posts``` folder and call it ```_post.html.haml```.  In that file, you want to tap away at your keyboard until the following code appears:

```haml
.posts-wrapper
  .post
    .post-head
      .thumb-img
      .user-name
        = post.user.user_name
      .time-ago
        = time_ago_in_words post.created_at
    .image.center-block
      = link_to (image_tag post.image.url(:medium), class:'img-responsive'), post_path(post)
    .post-bottom
      .caption
        .caption-content
          .user-name
            = post.user.user_name
          = post.caption
        .comments{id: "comments_#{post.id}"}
    .comment-like-form.row
      .like-button.col-sm-1
        %span(class="glyphicon glyphicon-heart-empty")
      .comment-form.col-sm-11
```

Good?  Good.  

You might notice something a bit weird with the above code though.  We’re referring to post without an **@** instance variable prefixed.  Why would that be the case?  

It gives us flexibility with how we can use this partial.  In the code snippets below, you’ll notice that we pass the ```@post``` variable as ```post``` to this partial view for our show view.  This means that  each reference to post will actually reference ```@post``` as required.  In our index view, we’ll pass something as well, check it out below.

Also, the id we’re using for the 'comments' div seems a bit strange, doesn’t it?  Well, that's used for our AJAX functionality that was built in the [original article](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/), and not this version.  Feel free to delete it if you don't intend on incorporating AJAX.  

**Let’s get fixing our index and show views now.**

Adjust your ```index.html.haml``` view like so:

```haml
.posts-wrapper.row
  -@posts.each do |post|
    = render 'post', post: post
```

And adjust your ```show.html.haml``` like so:

```haml
= render 'post', post: @post
.text-center.edit-links
  - if @post.user.id == current_user.id
    = link_to 'Cancel', posts_path
    |
    = link_to 'Edit Post', edit_post_path(@post)
  - else
    = link_to 'Cancel', posts_path
```

Great!  Now, this is going to look a bit... average at best at the moment.  Copy and paste the following scss to your ```app/assets/stylesheets/application.scss``` file.

```scss
 body {
   background-color: #fafafa;
   font-family: proxima-nova, 'Helvetica Neue', Arial, Helvetica, sans-serif;
 }

 /* ## NAVBAR CUSTOMISATIONS ## */

 .navbar-brand {
   a {
     color: #125688;
   }
 }

 .navbar-default {
   background-color: #fff;
   height: 54px;
   .navbar-nav li a {
     color: #125688;
   }
 }

 .navbar-container {
   width: 70%;
   margin: 0 auto;
 }

 /* ## POST CUSTOMISATIONS ## */

 .posts-wrapper {
   padding-top: 40px;
   margin: 0 auto;
   max-width: 642px;
   width: 100%;
 }

 .post {
   background-color: #fff;
   border-color: #edeeee;
   border-style: solid;
   border-radius: 3px;
   border-width: 1px;
   margin-bottom: 60px;
   .post-head {
     flex-direction: row;
     height: 64px;
     padding-left: 24px;
     padding-right: 24px;
     padding-top: 24px;
     color: #125688;
     font-size: 15px;
     line-height: 18px;
     .user-name, .time-ago {
       display: inline;
     }
     .user-name {
       font-weight: 500;
     }
     .time-ago {
       color: #A5A7AA;
       float: right;
     }
   }
   .image {
     border-bottom: 1px solid #eeefef;
     border-top: 1px solid #eeefef;
   }
 }

 .post-bottom {
   .user-name, .comment-content {
     display: inline;
   }
   .caption {
     margin-bottom: 7px;
   }
   .user-name {
     font-weight: 500;
     margin-right: 0.3em;
     color: #125688;
     font-size: 15px;
   }
   .user-name, .caption-content {
     display: inline;
   }
   #comment {
     margin-top: 7px;
     .user-name {
       font-weight: 500;
       margin-right: 0.3em;
     }
     .delete-comment {
       float: right;
       color: #515151;
     }
   }
   margin-bottom: 7px;
   padding-top: 24px;
   padding-left: 24px;
   padding-right: 24px;
   padding-bottom: 10px;
   font-size: 15px;
   line-height: 18px;
 }

 .comment_content {
   font-size: 15px;
   line-height: 18px;
   border: medium none;
   color: #4B4F54;
 }

 .comment-like-form {
   padding-top: 24px;
   margin-top: 13px;
   margin-left: 24px;
   margin-right: 24px;
   min-height: 68px;
   align-items: center;
   border-top: 1px solid #EEEFEF;
   flex-direction: row;
   justify-content: center;
 }


 /* ## Wrapper and styling for the new and edit views ## */

 .form-wrapper {
   width: 60%;
   margin: 20px auto;
   background-color: #fff;
   padding: 40px;
   border: 1px solid #eeefef;
   border-radius: 3px;
 }

 .edit-links {
   margin-top: 20px;
   margin-bottom: 40px;
 }
```

Beautiful!  Check it out in your browser by running your server if you haven’t already.

#### Time to keep going with that comment functionality...

Let’s first think our functionality out in pseudo-code.

```ruby
# Create a user
# Create a post  belonging to that user
# Sign in as that user
# Visit the root route
# Find the first comment box and write ';P'
# Click the 'Submit' button
# Expect the page to contain your brilliant comment
```

Pretty simple, right?  Create a new spec file under the features folder called ```creating_new_comments_spec.rb```.  How will you write the spec with Rspec / Capybara?  Well you’re about to find out, because it’s:

 ![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

Here’s my creating comment spec!

```ruby
require 'rails_helper'

feature 'Creating Comments' do
  scenario 'can comment on an existing post' do
    user = create :user
    post = create(:post, user_id: user.id)
    sign_in_with user
    visit '/'
    fill_in 'Comment', with: ';P'
    click_button 'Submit'
    expect(page).to have_css("div.comments#{post.id}", text: ';P')
  end
end
```

___

Now that you’ve got a spec to run, it’s time to build out this feature!  Remember, refer to the [original non-tested version](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/) for reference when required!


### RSPEC TIME!

*What to expect as you BDD this feature*

- No comment field?  Well you’re going to need a model to store the comments in (you’ll only want a ```content:string``` column and a ```user:references``` column), a controller for the comments and a form within your ```_post.html.haml``` partial that has a ’Submit’ button...
- Still not getting any comments to show?  Are you showing the comments for each post?  You’ll need to either create a comments partial for each comment, or iterate through them as a part of your ```_post.html.haml``` partial.
- Still nothing!  Have you tried creating a comment in your console yet?  Here’s how you can do it:

```ruby
# set the post on which to comment, this assumes you have one
post = Post.last
# now, build the comment for that post, this assumes you have a user with the id of 1
comment = post.comments.create({content: 'nice post brosef!', user_id: '1'})
```

Does that work?  If not, you’ve missed something along the way.  Fear not though, refer back to the [non-tested version](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/) for reference!

Easy enough!  It’s just the C in CRUD once again.  Things get a little trickier if you decide to implement AJAX submission but even then, you’ll have little trouble with that big brain of yours.

Let’s now give our users the ability to delete their posts the morning after their booze fuelled spree.

#### Comment deletion is a bit important

Yeah, you were super creepy in that comment, it’s best you delete it before anyone sees it! 

Oh wait, you can’t... that’s unfortunate for you.  

If you’re quick though, we might build the functionality in time!

Let’s pseudo-code a feature that let’s our users delete their own comments:

```ruby
background do
# create user_one from factory
# create user_two from factor with a different user name & email
# create a post from factory belonging to user_one
# create a comment from factory for the post created by user_two
# sign in as user_two
end
scenario
# visit the root path
# delete the offending comment belonging to you.
# expect that the comment no longer exists
end
```

We’ll ensure that users can’t delete comments belonging to others soon, but let’s write the test for this first.  Create a new spec file under ```/features``` called ```deleting_comments_spec.rb```.  You’ll also have to create your new Comments factory.

 ![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

Here’s my spec:

```ruby
require 'rails_helper'

feature 'Deleting comments' do
  background do
    user = create :user
    user_two = create(:user, id: 2, email: 'hi@hi.com', user_name: 'bigrigoz')
    post = create :post
    comment = create(:comment, user_id: user_two.id, post_id: post.id)
    sign_in_with user_two
  end
  scenario 'user can delete their own comments' do
    visit '/'
    expect(page).to have_content('Nice post!')
    click_link 'delete-1' # Dynamically add the id in your view
    expect(page).to_not have_content('Nice post!')
  end
end
```

___


### RSPEC TIME!

*What can you expect from your tests?*

- You *should* have the 'Nice post!' string on the page thanks to the last section of this guide, what you might not be able to find is the 'delete' id / link.  Create a link\_to helper method within your comments that links to the DELETE request of the specific comment it’s referring to.
- Rspec will then complain about the fact that there’s no 'destroy' action within your comments controller.  Create an empty destroy action.
- And then... missing a template?? We don’t want a template for the destroy action, we want it to destroy the comment and then redirect us back to the root path.  Implement that code within the destroy action.

Now, it’s great that we can delete comments willy nilly but let’s ensure that from here on in, we can only destroy the comments that belong to us.

#### Protecting our witty comments

Our comments deserve to be kept, they’re genius!  Let’s think about how we could write a test so that only the owner of a comment can delete it.

```ruby
# create a new comment via factories that’s owned by the first user (we’re logging in as the second user)
# expect it to exist
# expect to not see the delete link for that comment
```

Your turn now, transform that pseudo-code into another scenario within that same 'deleting comments' spec file.

 ![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

Well, here’s my whole spec file below to get some extra context.  I’ve added another comment to my background block so that we now have two comments on that post, each one belonging to each user.

```ruby
require 'rails_helper'

feature 'Deleting comments' do
  background do
    user = create :user
    user_two = create(:user, id: 2,
                             email: 'hi@hi.com',
                             user_name: 'bigrigoz')
    post = create :post
    comment = create(:comment, user_id: user_two.id,
                               post_id: post.id)
    comment_two = create(:comment, id: 2,
                                   post_id: post.id,
                                   content: 'You guys are too kind xo')
    sign_in_with user_two
  end
  scenario 'user can delete their own comments' do
    visit '/'
    expect(page).to have_content('Nice post!')
    click_link 'delete-1'
    expect(page).to_not have_content('Nice post!')
  end
  scenario 'user cannot delete a comment not belonging to them via the ui' do
    visit '/'
    expect(page).to have_content('You guys are too kind xo')
    expect(page).to_not have_css('#delete-2')
  end
end
```

___

### RSPEC TIME!

*What to expect?*

- Well, user\_two, the logged in user is going to be able to see the 'delete-2' id, because we haven’t written any logic whatsoever to prevent it.  Write some now within your view.  Think in terms of 'if the current user is equal to the comment user, show them the form'.

**Guess what?  user\_two can still delete the first users comments!**

Outrageous!  But how?  Well, if user\_two is sneaky enough, he might be able to send a DELETE request to the posts specific comment, even if he’s not the owner!

Here’s the concept in pseudo code.

```ruby
# visit the root path
# expect user one’s comment to exist
# send a DELETE request to the comment’s path
# expect the page to have a flash message ’That doesn’t belong to you!’
# expect user one’s comment to still exist
```

Write up the scenario now.  You might have to google how to send specific requests via capybara unless you’re already incredible.

 ![Your turn!](/content/images/2015/06/YourTurn-1.png)

___

Here’s my new scenario only:
```ruby
  scenario 'user cannot delete a comment not belonging to them via urls' do
    visit '/'
    expect(page).to have_content('You guys are too kind xo')
    page.driver.submit :delete, "posts/1/comments/2", {}
    expect(page).to have_content("That doesn't belong to you!")
    expect(page).to have_content('You guys are too kind xo')
  end
```

___

### RSPEC TIME!

*What to expect?*

- user\_two is going to be able to delete that comment via the url and the DELETE request.  You’ll have to block them in the controller by ensuring only the owner of a post can delete it.

## Finishing up for now

Incredible work!  You must be getting pretty comfortable with writing tests by now.  Along the way you’ve been building confidence and skill with Rspec, Capybara and FactoryGirl as well as with the Rails error messages and what they mean.

Please not this guide wasn’t exhaustive and we didn’t touch on AJAXing the comments.  Please refer to the [non-tested guide](http://www.devwalks.com/lets-build-instagram-with-rails-like-me-and-tell-me-im-beautiful/) if you’re interested in that.  

Also note that you should also add some validations to your new Comment model.  You don’t want to accept comments that either have an empty id or content field and you could even set a minimum character length on the content field if you wished.

In the upcoming articles, the format is going to be a little different.  No longer am I going to write a separate post for both the BDD and non-tested versions, I’m going to be combining them into one and also building fewer features per article so as to keep the content as to the point as possible.

I hope you’ve enjoyed the ride so far!  If you’d like to jump aboard and be informed about future parts of this guide, or even completely new guides that will be released in the future, please sign up below and you’ll be emailed the instant my fingers leave my keyboard.  I swear.
