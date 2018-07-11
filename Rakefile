  require 'rubygems'
  require 'rake'
  require 'time'
  require 'active_support/core_ext/string/strip'

  SOURCE = '.'.freeze
  CONFIG = {
    'layouts' => File.join(SOURCE, '_layouts'),
    'posts' => File.join(SOURCE, '_posts'),
    'post_extension' => 'md'
  }.freeze

  def agree?(msg)
    answer = get_stdin("#{msg} ").downcase[0]
    true if answer == 'y'
  end

  def get_stdin(msg)
    print msg
    STDIN.gets.chomp
  end

  namespace :gen do
    desc "Create a new post | Args: title='' cat=''"
    task :post do
      abort("Post creation aborted: '#{CONFIG['posts']}' directory not found.") unless FileTest.directory?(CONFIG['posts'])
      title = ENV['title'] || 'new-post'
      categories = ENV['cat'] || ''
      slug = title.downcase.tr(' ', '-')

      begin
        date = Time.now.strftime('%Y-%m-%d')
      rescue
        raise 'Error - date format must be YYYY-MM-DD'
      end

      filename = File.join(CONFIG['posts'], "#{date}-#{slug}.#{CONFIG['post_extension']}")
      if File.exist?(filename)
        msg = "#{filename} already exists, do you want to overwrite it?"
        abort('Post creation aborted') if agree?(msg) == false
      end

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

      if File.size(filename) > 0
        puts "New post created: #{date}-#{slug}.#{CONFIG['post_extension']}"
      end
    end
  end