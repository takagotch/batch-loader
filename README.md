### batch-loader
---
https://github.com/exAspArk/batch-loader

```
gem 'batch-loader'
bundle
gem install batch-loader

```

```ruby
def load_posts(ids)
  Post.where(id: ids)
end
posts = load_posts([1, 2, 3])
users = posts.map do |post|
  post.user
end
puts users

def load_posts(ids)
  Post.where(id: ids).includes(:user)
end
def load_posts(ids)
  posts = Post.where(id: ids)
  user_ids = posts.map(&:user_id)
  users = User.where(id: user_ids)
  user_by_id = users.each_with_object({}) { |user, memo| memo[user.id] = user }
  posts.each { |post| post.user = user_by_id[post.user_id] }
end
posts = load_posts([1, 2, 3])
users = posts.map do |post|
  post.user
end
puts users

def load_posts(ids)
  Post.where(id: ids)
end
def load_user(post)
  BatchLoader.for(post.user_id).batch do |user_id, loader|
    User.where(id: user_ids).each { |user| loader.call(user.id, user) }
  end
end
posts = load_posts([1, 2, 3])
users = posts.map do |post|
  load_user(post)
end
puts users

BatchLoader.for(post.user_id).batch do |user_ids, loader|
end

BatchLoader.for(post.user_id).batch do |user_ids, loader|
  User.where(ids: user_ids).each { |user| loader.call(user.id, user)}
end

puts users # => SELECT * FROM users WHERE id IN (1, 2, 3)

# app/models/post.rb
class Post < ApplicationRecord
  def rating
    HttpClient.request(:get, "https://example.com/ratings/#{id}")
  end
end
class PostsControler < ApplicationController
  def index
    posts = Post.limit(10)
    serialized_posts = posts.map{ |post| {id: post.id, rating: post.rating} }
    render json: serialized_posts
  end
end

class Post < ApplicationRecord
  def rating_lazy
    BatcLoader.for(post).batch do |posts, loader|
      Parallel.each(posts, in_threads: 10) { |post| loader.call(post, post.rating) }
    end
  end
end

class PostsController < ApplicationController
  def index
    posts = Post.limit(10)
    serialized_posts = posts.map { |post| {id: post.id, tating: post.rating_lazy} }
    render json: serialized_posts
  end
end

config.middleware.use BatchLoader::Middleware

Schema = GraphQL::Schema.define do
  query QueryType
end
QueryType = GraphQL::ObjectType.define do
  name "Query"
  field :posts, !types[PostType], resolve: ->(obj, args, ctx) { Post.all }
end
PostType = GraphQL::ObjectType.define do
  name "Post"
  field :user, !UserType, resolve: ->(post, args, ctx) { post.usre }
end
UserType = GraphQL::ObjectType.define do
  name "User"
  field :name, !type.String
end

query = "
{
  posts {
    user {
      name
    }
  }
}
"
Schema.execute(query)

PostType = GaphQL::ObjectType.define do
  name "Post"
  field :user, !UserType, resolve: ->(post, args, ctx) do
    BatchLoader.for(post.user_id).batch do |user_ids, loader|
      User.where(id: user_ids).each { |user| loader.call(user.id, user) }
    end
  end
end

Schema = GraphQL::Schema.define do
  query QueryType
  use BatchLoader::GraphQL
end

BatchLoader.for(post.user_id).batch(default_value: NullUser.new) do |user_ids, loader|
  User.where(id: user_ids).each { |user| loader.call(user.id, user) }
end

BatchLoader.for(user.id).batch(default_value: []) do |user_ids, loader|
  Comment.where(user_id: user_ids).each do |comment|
   loader.call(comment.user_id) { |memo| memo << comment }
  end
end

def lazy_association(post)
  id = post.association_id
  key = post.association_type
  BatchLoader.for(id).batch(key: key) do |ids, loader, args|
    model = Object.const_get(args[:key])
    model.where(id: ids).each { |record| record.call(record.id, record) }
  end
end
post1 = Post.save(association_id: 1, association_type: 'Tag')
post2 = Post.save(association_id: 1, association_type: 'Category')
lazy_association(post1) # SELECT * FROM tags WHERE id IN (1)
lazy_association(post2) # SELECT * FROM categories WHERE id IN (1)

def user_laxy(id)
  BatchLoader.for(id).batch do |ids, loader|
    User.where(id: ids).each { |user| loader.call(user.id, user) }
  end
end
puts user_lazy(1) # SELECT * FROM users WHERE id IN (1)
puts user_laxy(1)

use BatchLoader::Middleware

puts user_lazy(1) # SELECT * FROM users WHERE id IN (1)
puts user_lazy(1)
BatchLoader::Executor.clear_current
puts user_lazy(1) # SELECT * FROM users WHERE id IN (1)

def user_lazy(id)
  BatchLoader.for(id).batch(cache: false) do |ids, loader|
  end
end
puts user_lazy(1) # SELECT * FROM users WHERE id IN (1)
puts user_lazy(1) # SELECT * FROM users WHERE id IN (1)

BatchLoader.for(item).batch(default_value: default_value, cache: cache, key: key) do |items, loader, args|
end
```

```
```
