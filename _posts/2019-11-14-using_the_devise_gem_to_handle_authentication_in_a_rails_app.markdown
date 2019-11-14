---
layout: post
title:      "Using the Devise Gem to Handle Authentication in a Rails App"
date:       2019-11-14 14:53:07 +0000
permalink:  using_the_devise_gem_to_handle_authentication_in_a_rails_app
---

One of the most important features of any app is authentication. In its simplest form, you want your user to be able to sign up, log in, and log out securely. You could rely on Rails’ built in method `has_secure_password` to provide that functionality, but then you’re required to add necessary password validations and helper methods yourself. And what if your user forgets their password? That quickly gets complicated. While it is important to understand the basic steps involved in authentication, once you do, there is a much simpler way to achieve it, and much more.

The Devise gem was built to handle all of the above for you, plus it provides sign in and sign up forms and a bunch of methods to get you started right off the bat. You can choose to use all of its ten modules or only use what you need, making it easily customizable. Devise also provides the ability to interface with more complex authentication systems for working with APIs like OAuth, which means you can give your user the option to log in to your app through a third party like Google or GitHub. Plus, getting set up is pretty easy.

1. Add the following to your Gemfile:
`gem 'devise'`

2. Then run `bundle install`

3. Next, run:
`$ rails generate devise:install`

4. Generate your User (or Admin) model:
`$ rails generate devise USER`

5. Check your model for additional configuration options and add them if needed, then check your migration file and uncomment any options you added.

6. Run `rails db:migrate`

7. Generate your views:
`$ rails generate devise:views`
Note: If you are setting up your app to have more than one Devise model (such as User and Admin) you need to set config.scoped_views = true inside the config/initializers/devise.rb file, then you are able to have role-specific views, such as users/sessions/new and admins/sessions/new. You can also generate scoped views by running:
`$ rails generate devise:views users`

8. Generate a RegistrationsController that inherits from Devise::RegistrationsController

9. Permit the attributes you want in your strong params. For example:
```
private
def sign_up_params 
  params.require(:user).permit(:name, :email, :password, :password_confirmation)
end
def account_update_params
  params.require(:user).permit(:name, :email, :password, :password_confirmation, :current_password)
end
```

10. In your routes.rbfile, add:
`devise_for :users, :controllers => { registrations: 'registrations'}`

Note: If you are going to use OmniAuth, you will need to change the above to:

```
devise_for :users, controllers: { registrations: ‘registrations’, omniauth_callbacks: ‘users/omniauth_callbacks’ }
and specify your configuration in config/initializers/devise.rb:
config.omniauth :github, 'APP_ID', 'APP_SECRET', scope: 'user,public_repo'
```

This is a basic setup, but you can read more on how to customize Devise and utilize the included helper methods [here](https://github.com/plataformatec/devise)!
