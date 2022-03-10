---
layout: post
title:  "Removing Devise Registrations"
date:   2019-03-13 12:00:00 -0500
tags: ["Ruby on Rails", "Devise"]
---

I find myself quite often building apps that require users, but should not have registration open to the public. The easy solution is just to remove the routes and devise functionality.

So in order to help my future self, and anyone else who might be interested, I decided to outline my steps here.

1. Update the Devise definitions in the User model:
    ```ruby
    # original
    class User < ApplicationRecord
      devise :database_authenticatable, :registerable, :recoverable, :rememberable,
        :validatable

      #...
    end

    # updated
    class User < ApplicationRecord
      devise :database_authenticatable, :recoverable, :rememberable, :validatable

      #...
    end
    ```
2. Update the routes and remove most registration routes:
    ```ruby
    # original
    Rails.application.routes.draw do
      devise_for :users

      #...
    end

    # updated
    Rails.application.routes.draw do
      devise_for :users, skip: [:registrations]
      as :user do
        # These routes are used once logged in for updating user accounts
        get 'users/edit', to: 'devise/registrations#edit', as: :edit_user_registration
        put 'users', to: 'devise/registrations#update', as: :user_registration
        delete 'users', to: 'devise/registrations#destroy', as: :destroy_user_registration
      end

      #...
    end
    ```

And you're done!
