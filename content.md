# Photogram Industrial Part 3: Starting user interface

## Walkthrough video

<div class="bg-red-100 py-1 px-5" markdown="1">
**Please note**, the video is from a previous iteration of the project, so there are some differences:

- I am using Gitpod as my cloud editor, so the interface looks a bit different.
- Anything contained in the project "README" is now contained in this Lesson
- I use `bin/server` to start my live app preview, _you_ should use `bin/dev`
- I use `rails sample_data`, _you_ should use `rake sample_data`
</div>

Did you read the differences above? Good! Then [here is a walkthrough video for this project.](https://share.descript.com/view/KkN3XUdeop3)

**As you watch the video, pause it frequently, read the associated text, and type out the code.**

## Getting started

Let's continue with `photogram-industrial`, keeping in mind a rough target to work towards:

[pg-industrial-2-final.herokuapp.com](https://pg-industrial-2-final.herokuapp.com/)

So navigate to `github.com/codespaces` and reopen your `photogram-industrial` codespace to continue building on what you accomplished in _Photogram Industrial Parts 1 and 2_.

## Starting on the interface

Now that we have our backend in a great place, let's launch into building the interface. With our sample data and association accessor methods at our fingertips, you'll see that building out the interface is going to be a breeze. 

The first thing we're going to do is create a new git branch for our work.

First, run a `git status` (or, as we've set it up, just `g`), to see what branch you're on:

```
% g
On branch rb-photogram-industrial-2
Your branch is up to date with 'origin/rb-photogram-industrial-2'.

nothing to commit, working tree clean
```

I'm currently on the branch where we built out our whole backend: `rb-photogram-industrial-2`. I want to build on top of this rather than switching to the `main` branch, which doesn't contain our backend work. Let's create a new branch off of the previous work to start building the frontend.

At the terminal, we'll `git checkout -b <branch-name>`, or just `g cob` for short:

```
main % git cob rb-starting-on-ui 
Switched to a new branch 'rb-starting-on-ui'
rb-starting-on-ui %
```

The bottom line is: branches are easy to create and cheap. If you branch off of another branch, you can easily merge them later on, because they share the same history. (Merging becomes slightly more involved if you make additional commits to the parent branch, causing the histories to diverge.)

Once you're on the new branch, get the app running with `bin/dev`. As we work, we'll keep in mind the target interface:

[pg-industrial-2-final.herokuapp.com](https://pg-industrial-2-final.herokuapp.com/)

We haven't done anything to the interface yet, so let's begin by giving ourselves a root route in `routes.rb`:

```ruby{4}
# config/routes.rb

Rails.application.routes.draw do
  root "photos#index"
  
  devise_for :users
  
  resources :comments
  resources :follow_requests
  resources :likes
  resources :photos
end
```

Also, I've changed the ordering of the routes. I usually keep the root route at the top, followed by the Devise routes. My convention is to alphabetize the routes, unless it's something particularly important, like Devise. It's worth noting, routes are matched top-down, so if there are conflicting routes (e.g., `/movies/new` and `/movies/:id`), the more specific route should be above the more general.

Now we can visit `/` in our live app preview, which should show us the photo index page. We have the `sample_data` task written, so run that with `rake sample_data`. If there wasn't already, you'll now see a bunch of diverse data in the app for us to look at as we build out the interface.

You may note that there is already some styling of the page. That's because we used `scaffold`s to generate our resources, and that added the file `app/assets/stylesheets/scaffolds.scss` (and individual, but empty files for each resource, like `photos.scss`). We're going to be using Bootstrap, so you can delete `scaffolds.scss` now.

<div class="bg-red-100 py-1 px-5" markdown="1">

In the more recent version of the project, we disabled the scaffold default CSS file generation, so you shouldn't need to delete anything.
</div>

<aside markdown="1">

From now on, Bootstrap is the minimum styling we want to add to anything we build. Then we could hand craft styles on top of that with actual CSS. Nowadays, handcrafting styles actually means that we would use something like [Tailwind CSS](https://tailwindcss.com/), which is a lower-level framework than Bootstrap. Tailwind gives you utility classes, from which you craft your own components.
</aside>

Please note that we're using Bootstrap v4 in the video. The official Bootstrap docs now show v5 by default, so when referring to the docs, you can switch to [v4.6 using the dropdown in the top-right](https://getbootstrap.com/docs/4.6/getting-started/introduction/). However, this isn't strictly necessary and you should be able to use the most up-to-date bootstrap version in your project without getting confused.

## Application layout

Let's begin by giving ourselves some navigation. We'll put this in the application layout, and, because we want to keep our code organized and modular, we'll use a partial:

```erb{6}
<!-- app/views/layouts/application.html.erb -->

<!-- ... -->
  <body>

    <%= partial "shared/navbar" %>

    <%= yield %>
  </body>
</html>
```

Create that folder and file: `app/views/shared/_navbar.html.erb` (don't forget the underscore on `_navbar` in the filename).

With that file open, fill it with the [Bootstrap kitchen sink example](https://getbootstrap.com/docs/4.6/components/navbar/). Now go back to the live app and see if it worked.

We see something popped up, but it looks like there's no styling on the page now. Why?

We forgot to include the external stylesheet link to Bootstrap! Let's also add that CDN stuff as a partial to our application layout:

```erb{14}
<!-- app/views/layouts/application.html.erb -->

<!DOCTYPE html>
<html>
  <head>
    <title>Photogram Industrial</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag "application", "data-turbo-track": "reload" %>
    <%= javascript_importmap_tags %>

    <%= render partial: "shared/cdn_assets" %>
  </head>
<!-- ... -->
```

And in the `app/views/shared/_cdn_assets.html.erb`, we can plop in our stylesheets:

```erb
<!-- app/views/shared/_cdn_assets.html.erb -->

<!-- Connect Bootstrap CSS -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.3/dist/css/bootstrap.min.css">

<!-- Connect Bootstrap JavaScript and its dependencies -->
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.2.3/dist/js/bootstrap.min.js"></script>

<!-- Connect Font Awesome -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.1.1/js/all.min.js"></script>
```

Now refresh the live app, and we should see the correctly styled page with the navbar we added. Let's also quickly put the `yield` statement in a container to give our page some breathing room:

```erb{8-10}
<!-- app/views/layouts/application.html.erb -->

<!-- ... -->
  <body>

    <%= partial "shared/navbar" %>

    <div class="container">
      <%= yield %>
    </div>

  </body>
</html>
```

Let's make a git commit, perhaps: `g acm "Added navbar and CDN assets"`.

## Starting navbar styling

Next, we'll need to make some changes to that copy-pasted navbar to suit our purposes. We want to avoid `<a>` elements, because we have our `link_to` helper method. We can start by changing the homepage link at the top of the navbar:

```erb{4-5}
<!-- app/views/shared/_navbar.html.erb -->

<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <!-- <a class="navbar-brand" href="#">Navbar</a> -->
  <%= link_to "Photogram", root_path, class: "navbar-brand" %>
  
  <!-- ... -->
</nav>
```

From here, my goal is to have the "Dropdown" menu contain all of the resources (their index pages will be fine), and for the sign in links to be on the right side of the navbar where the "Search" currently is. 

For the "Dropdown" menu, we just want some quick and dirty links to help us navigate, so I won't worry about using `link_to`. Here's how your navbar should look for the dropdown menu:

```erb{5-14}
<nav class="navbar navbar-expand-lg navbar-light bg-light">
    <!-- ... -->
    
    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <ul class="navbar-nav mr-auto">
        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown" aria-expanded="false">
            Resources
          </a>
          <div class="dropdown-menu">
            <a class="dropdown-item" href="/photos">Photos</a>
            <a class="dropdown-item" href="/comments">Comments</a>
            <a class="dropdown-item" href="/follow_requests">Follow Requests</a>
            <a class="dropdown-item" href="/likes">Likes</a>
          </div>
        </li>
      </ul>
      <!-- ... -->
```

Try out the dropdown links from the "Resources" navbar button. Can you reach the index pages for each resource? Do you see the data from `sample_data`?

It might also be nice to add some padding to the navbar text so it's in-line with our other page content. We can do that by wrapping all of the _content_ of our navbar in another `div.container`:

```erb{2,5}
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container">
    <%= link_to "Photogram", root_path, class: "navbar-brand" %>
      <!-- ... -->
  </div>
</nav>
```

Why not make a commit?

## User account links

We would also like some user account links (sign in, sign out, etc.) as implemented by devise. Devise gives us a number of route helpers for these special paths. You can examine them with `rails routes`. The important items are:

 - Edit profile: `edit_user_registration_path`
 - Sign out: `destroy_user_session_path`
 - Sign in: `new_user_session_path`
 - Sign up: `new_user_registration_path`

We can start this by adding the `"nav-item"`s in an unstructured list at the end of the navbar, in place of the "Search" form:

```erb{4-17}
<!-- app/views/shared/_navbar.html.erb -->

<!-- ... -->
      <ul class="navbar-nav">
        <li class="nav-item">
          <%= link_to "Edit profile", edit_user_registration_path, class: "nav-link" %>
        </li>
        <li class="nav-item">
          <%= link_to "Sign out", destroy_user_session_path, data: { turbo_method: :delete }, class: "nav-link" %>
        </li>
        <li class="nav-item">
          <%= link_to "Sign in", new_user_session_path, class: "nav-link" %>
        </li>
        <li class="nav-item">
          <%= link_to "Sign up", new_user_registration_path, class: "nav-link" %>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

For the "Sign out" link, we also needed to add the option `data: { turbo_method: :delete }`, because this is a DELETE HTTP request.

<div class="bg-red-100 py-1 px-5" markdown="1">

In the video, I use `method: :delete`; due to the Rails 7 update, you should be using `data: { turbo_method: :delete }`.
</div>

Try out the navbar links. Everything working? 

The links should work, but we need to add some conditionals here. The user should see different links depending on whether they are signed in or not.

With devise, a helper method has been defined called `current_user`. In general, it's better to avoid re-used instance variables throughout our app by using helper methods that read and use a given instance variable. That's why devise gives us `current_user`.

What we would normally do is something like: `if current_user.present?`. In fact, devise gives us another helper method for that chained code, which reads somewhat better: `if user_signed_in?`, so let's use that:

```erb{5,7:(26-54),13,21}
<!-- app/views/shared/_navbar.html.erb -->

<!-- ... -->
      <ul class="navbar-nav">
        <% if user_signed_in? %>
          <li class="nav-item">
            <%= link_to "Edit #{current_user.username}", edit_user_registration_path, class: "nav-link" %>
          </li>
          <li class="nav-item">
            <%= link_to "Sign out", destroy_user_session_path, method: :delete, class: "nav-link" %>
          </li>

        <% else %>
          <li class="nav-item">
            <%= link_to "Sign in", new_user_session_path, class: "nav-link" %>
          </li>
          <li class="nav-item">
            <%= link_to "Sign up", new_user_registration_path, class: "nav-link" %>
          </li>

        <% end %>
      </ul>
    </div>
  </div>
</nav>
```

We also added the `current_user`s username to the "Edit profile" copy, to give us some additional visual notice that we're signed in.

With that conditional, if we return to our live app, then we should only see the "Sign in" and "Sign up" links. 

Now's a good time to commit!

## Flaw in `sample_data`

On the "Sign in" page, it would be good if we knew one of our user accounts, so that we could log right in and keep testing the app. This is a flaw in our sample data task, so let's correct it now.

We'll do that by adding a known user to our sample data:

```ruby{16,18-20,22,24}
# lib/tasks/dev.rake

desc "Fill the database tables with some sample data"
task sample_data: :environment do
  p "Creating sample data"
  starting = Time.now

  if Rails.env.development?
    FollowRequest.delete_all
    Comment.delete_all
    Like.delete_all
    Photo.delete_all
    User.delete_all
  end

  usernames = Array.new { Faker::Name.first_name }

  usernames << "alice"
  usernames << "bob"

  usernames.each do |username|
    User.create(
      email: "#{username}@example.com",
      password: "password",
      username: username.downcase,
      private: [true, false].sample,
    )
  end
# ...
```

We created an array of usernames, added some known values to it (`"alice"` and `"bob"`), and used that to generate users.

Now re-run `rake sample_data`, return to the live app, and try to sign in with the user: `alice@example.com` and password: `password`.

Working? Do we see the edit and sign out links now? Good! Time to commit.

```
g acm "Better sample data"
```

And we should push this branch up to github for safe keeping, and for opening a PR:

```
git push --set-upstream origin rb-starting-on-ui
```

(Subsequent pushes can be done with `g p`. The `--set-upstream` flag was just for the first push.)

## Force sign in

Our navbar is in good shape. 

Let's force that somebody is signed in to view anything else, because I'm going to start to modify all of my pages, and I'm going to assume that there's someone signed in so that I can start calling methods on `current_user`.

We don't want to put `user_signed_in?` combined with conditionals all over the app. Rather, let's add a force sign in to the `ApplicationController`:

```ruby{4}
# app/controllers/application_controller.rb

class ApplicationController < ActionController::Base
  before_action :authenticate_user!
end
```

We use the method `before_action` to call the method `:authenticate_user!`, provided by Devise.

Now if you sign out in the live app and try to navigate to any of the index pages, you should be forced back to the log in page. But it would also be nice to have some flash messages to explain why the user can't move around without signing in!

## Flash messages

We'll add flash messages as usual with partials in the application layout and [Bootstrap alert boxes](https://getbootstrap.com/docs/4.6/components/alerts/) in the partial file.

We can use some [dismissable flash messages](https://getbootstrap.com/docs/4.6/components/alerts/#dismissing) as well, so the message can be closed by the user if they want.

We might note that the difference between the alert code comes down to putting `alert` vs. `notice` in the body of the message, and changing the class to `danger` vs. `success`. That means, we can get a little fancy, and use some local variables in our partial.

Starting with the copy-pasted, then modified partial `_flash.html.erb` that we create in the `app/views/shared/` folder:

```erb{3,4}
<!-- app/views/shared/_flash.html.erb -->

<div class="alert alert-<%= css_class %> alert-dismissible fade show" role="alert">
  <%= message %>
  <button type="button" class="close" data-dismiss="alert" aria-label="Close">
    <span aria-hidden="true">&times;</span>
  </button>
</div>
```

We will pass the type of alert box in with `<%= css_class %>` and the message in with `<%= message %>`.

Now in the application layout file, we can add those alerts with some conditional statements:

```erb{9-11,13-15}
<!-- app/views/layouts/application.html.erb -->

<!-- ... -->
  <body>

    <%= partial "shared/navbar" %>

    <div class="container">
      <% if notice.present? %>
        <%= partial "shared/flash", message: notice, css_class: "success" %>
      <% end %>
      
      <% if alert.present? %>
        <%= partial "shared/flash", message: alert, css_class: "danger" %>
      <% end %>
      
      <%= yield %>
    </div>
  </body>
</html>
```

Try the live app again and see the dismissable alerts in action. We probably want some breathing room though, because they're pressed up on the navbar right now. Let's just add a quick margin bottom `mb` to the navbar container:

```erb{3:(59-62)}
<!-- app/views/shared/_navbar.html.erb -->

<nav class="navbar navbar-expand-lg navbar-light bg-light mb-4">
  <!-- ... -->
```

Finally, our view templates that came with `scaffold` (like `app/views/photos/index.html.erb`) have some code at the top:

```erb
<p id="notice"><%= notice %></p>
```

We should go through the view templates and delete those, or just keep them in mind and be sure to delete them as we go through the rest of the interface design.

Let's commit that nice chunk of work:

```
g acm "Added alert and notice flash messages"
```

And push it up with:

```
g p
```

Now that we have Bootstrap styling, a navbar to get around, forced user sign in to ensure that `current_user` is not `nil`, and alert messages, we are ready to confidently build out the rest of our app.
