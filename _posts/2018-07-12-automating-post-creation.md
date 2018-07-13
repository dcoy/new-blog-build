---
layout: post
title: Automating Post Creation
subtitle: "Creating new Jekyll posts with Rake"
date: 2018-07-12 19:40:05
description: "Creating new Jekyll posts with Rake"
categories: [ruby, rake, automation]
---

I've been using Hugo to create my static site for some time, but decided to migrate _back_ over to Jekyll, as I love the Ruby community and the language itself. I was disappointed to learn that Jekyll does not have a subcommand to generate a new posts, so I decided to write my own Rake task go generate my new post, with included front matter.

## Inital Planning

To start, I wanted to define the location the command is run from, as well the configuration for posts directory and file format. 

{% highlight ruby %}

SOURCE = '.'.freeze
CONFIG = {
  'posts' => File.join(SOURCE, '_posts'),
  'post_extension' => 'md'
}.freeze

{% endhighlight %}

Next, I began to define my task. Initially, I had just described and written the task `:post`. This was fine for just creating a new post, however I wanted to be able to generate other types of data for my blog, so I wrapped the task in a namespace:

{% highlight ruby %}

namespace :gen do
  desc "Create a new post | Args: title='' cat=''"
  task :post do
    # Code here
  end
end

{% endhighlight %}

By doing so, I can extend the functionality of my Rakefile whenver I want to generate new content, by coding other tasks under the `:gen` namespace.

## Writing the task

I began by aborting the process immediately in the event the command is run outside the root directory (something I would like to shorten at a later date), since the `_posts` would not be found, setting the arguments to pass in, as well setting the format for the slug. I used to `tr` instead of `gsub` only because I'm replacing whitespace with a hyphen, and `gsub` is better suited to replace regex matches:

{% highlight ruby %}

abort("Post creation aborted: '#{CONFIG['posts']}' directory not found.") unless FileTest.directory?(CONFIG['posts'])
    title = ENV['title'] || 'new-post'
    categories = ENV['cat'] || ''
    slug = title.downcase.tr(' ', '-')

{% endhighlight %}

Next, I wanted to write a statement to catch any incorrect date formats:

{% highlight ruby %}

begin
  date = Time.now.strftime('%Y-%m-%d')
rescue
  raise 'Error - date format must be YYYY-MM-DD.'
end

{% endhighlight %}

Moving forward, I set the file identifier in memory, and write to it. The first version of the loop to write to the file wasn't clean, and lacked an "automated" workflow as I was asking for confirmation to overwrite a file if it already existed in the `_posts` directory:

{% highlight ruby %}

filename = File.join(CONFIG['posts'], "#{date}-#{slug}.#{CONFIG['post_extension']}")
if File.exist?(filename)
  msg = "#{filename} already exists, do you want to overwrite it?"
  abort('Post creation aborted') if agree?(msg) == false
end

puts "Creating new post: #{filename}"
File.open(filename, 'w') do |post|
  post.puts '---'
  post.puts 'layout: post'
  post.puts "title: \"#{title.tr('-', ' ')}\""
  post.puts 'subtitle: ""'
  post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M:%S')}"
  post.puts 'description: ""'
  post.puts "categories: [#{categories}]"
  post.puts '---'
end


{% endhighlight %}

This got the job done, but it wasn't clean nor automated, so I rewrote it to be a bit more concise:

{% highlight ruby %}

filename = File.join(CONFIG['posts'], "#{date}-#{slug}.#{CONFIG['post_extension']}")

puts "Creating new post: #{filename}"
File.open(filename, 'w') do |post|
  post.write(<<-EOF.strip_heredoc)
    ---
    layout: post
    title: #{title.tr('-', ' ')}
    subtitle: ""
    date: #{Time.now.strftime('%Y-%m-%d %H:%M:%S')}
    description: ""
    categories: [#{categories}]
    ---
  EOF
end

{% endhighlight %}

The `strip_heredoc` is called from the `active_support` gem library, as each time a new post was created, the YAML front matter was indented. 

Finally, I ran a simple validation on the size of the file to ensure the file was created successfully:

{% highlight ruby %}

if File.size(filename) > 0
  puts "New post created: #{date}-#{slug}.#{CONFIG['post_extension']}"
end

{% endhighlight %}

Thanks for reading!