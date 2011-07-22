# Get Setup for Ruby on Rails from Scratch

## Assumptions

I will assume you currently have Mac OSX 10.6, XCode / Developer Kit, Git, Growl, Textmate.

## Basic Install

### Install rvm:

    $ mkdir -p ~/.rvm/src/ && cd ~/.rvm/src && rm -rf ./rvm/ && git clone git://github.com/wayneeseguin/rvm.git && cd rvm && ./install

Add the following to your ~/.profile:

    # Load RVM
    [[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm"

Load your .profile:

    $ source ~/.profile
    $ rvm -v
    $ rvm list

### Install ruby 1.9.2:

    $ rvm install 1.9.2
    $ rvm 1.9.2 --default
    $ ruby -v
    $ rvm list
    $ rvm gemdir
    $ gem -v
    $ gem list

Make gem install not compile rdoc or ri, add the following to ~/.gemrc:
  
    gem: --no-ri --no-rdoc

### Install rails:

    $ gem install rails
    $ rails -v

## Install Postgres  

Use Homebrew (guide here - https://willj.net/2011/05/31/setting-up-postgresql-for-ruby-on-rails-development-on-os-x/)

OR

Get the [one click installer](http://www.postgresql.org/download/macosx) (> v 9.0.3), and run the app inside the dmg. (Note: I had to restart my system and re-run the installer). Use the default settings, set the password to postgres. Don't run stack builder at the end.

Add the following to your ~/.profile:

    # Add Postgres binaries to the path
    export PATH=$PATH:/Library/PostgreSQL/9.0/bin

Reload your ~/.profile:

    $ source ~/.profile

## Install Textmate Bundles

    $ mkdir -p ~/Library/Application\ Support/TextMate/Bundles/
    $ cd ~/Library/Application\ Support/TextMate/Bundles/

Install rspec textmate bundle:

    $ git clone git://github.com/rspec/rspec-tmbundle.git RSpec.tmbundle

Install Haml textmate bundle:

    $ git clone git://github.com/phuibonhoa/handcrafted-haml-textmate-bundle.git Haml.tmbundle

Install cucumber bundle:

    $ git clone git clone https://github.com/drnic/cucumber-tmbundle Cucumber.tmbundle

Bundles > Bundle Editor > Reload Bundles, in TextMate.

## Create a new rails application

Create a new app by cloning this repository, and running:

    $ rails new [myapp] -J -T -m /path/to/template.rb
    
Your new app has the following features.

* **Blueprint-CSS:** css reset, default styles, print styles, IE stylesheet, grid system, included in default layout
* **RSpec:** generators working
* **factory_girl:** generators working, loaded in rspec
* **jQuery:** replacing prototype, included in the default layout
* **Haml:** generators working, replacing default layout with a haml layout.
* **postgres:** (optional) configured with provided password
* **cucumber and capybara:** for integration testing
* **ruby debug**
* **awesome_print**
* **Metrical** for running metrics on code quality
* **annotate-models**

The template also generates a good .gitignore file, initializes an empty git repository, and cleans up some unnecessary rails files.

No additional setup tasks required.

## Tips for using your new rails application
    
* Debug your app by adding *debugger* in your code. Use *rails s --debug* to debug in server. Type *help* in debugger to learn commands.
* Use *ap* to print awesomely in the console.
* Annotate your models by running *annotate* at any time.
* Generate code quality metrics by running *metrical*.
* Run individual specs or feature files in textmate by typing: *cmd-r*.
    
## Bonus Setup

### Pimp Your Console

For a nice console prompt, logging to console, console indenting, setup your ~.irbrc to look like:

    %w{rubygems}.each do |lib| 
      begin 
        require lib 
      rescue LoadError => err
        $stderr.puts "Couldn't load #{lib}: #{err}"
      end
    end

    # Prompt behavior
    IRB.conf[:AUTO_INDENT] = true

    # Loaded when we fire up the Rails console
    # => Set a special rails prompt.
    # => Redirect logging to STDOUT.   
    if defined?(Rails.env)
      rails_env = Rails.env
      rails_root = File.basename(Dir.pwd)
      prompt = "#{rails_root}[#{rails_env.sub('production', 'prod').sub('development', 'dev')}]"
      IRB.conf[:PROMPT] ||= {}
      IRB.conf[:PROMPT][:RAILS] = {
        :PROMPT_I => "#{prompt}>> ",
        :PROMPT_S => "#{prompt}* ",
        :PROMPT_C => "#{prompt}? ",
        :RETURN   => "=> %s\n" 
      }
      IRB.conf[:PROMPT_MODE] = :RAILS
      # Redirect log to STDOUT, which means the console itself
      IRB.conf[:IRB_RC] = Proc.new do
        logger = Logger.new(STDOUT)
        ActiveRecord::Base.logger = logger
        ActiveResource::Base.logger = logger
        ActiveRecord::Base.instance_eval { alias :[] :find }
      end
    end
    
### Install Autotest and Spork for fast, continuous testing

First, make sure you have growl installed and running on login.

    $ gem install autotest-standalone
    $ gem install autotest-fsevent
    $ gem install autotest-growl

Setup autotest to use fsevent and growl. Create ~/.autotest with:
  	require 'autotest/fsevent'
  	require 'autotest/growl'

Make sure you have growl configured to accept the autotest application.

Run autotest:
    $ autotest

It will watch for changes to specs and source code of all your apps and run any required tests.

Install spork to increase your test speed. Follow the instructions [here](
http://www.rubyinside.com/how-to-rails-3-and-rspec-2-4336.html).

### Load jQuery and jQuery-UI from google

See: 

    http://code.google.com/apis/libraries/devguide.html 

and add script tags to load the desired libraries. Remove :defaults, and add the scripts you want to load manually.

jquery-ui css is hosted at:
      
    http://ajax.googleapis.com/ajax/libs/jqueryui/[UI.VERSION]/themes/[THEME-NAME]/jquery-ui.css

### Bookmark some essential pages

* [Rails Guides](http://guides.rubyonrails.org/) (Rails reference)
* [RSpec Reference](http://relishapp.com/rspec/)
* [Capybara Cheatsheet](http://richardconroy.blogspot.com/2010/08/capybara-reference.html), [original](https://gist.github.com/428105#file_capybara%20cheat%20sheet)
* [Capybara Reference](https://github.com/jnicklas/capybara)
* [Blueprint Cheatsheet](http://www.garethjmsaunders.co.uk/blueprint/downloads/blueprintcss-1-0-cheatsheet-4-2-gjms.pdf)
* [Blueprint Examples](http://www.blueprintcss.org/tests/)
* [Haml Reference](http://haml-lang.com/docs/yardoc/file.HAML_REFERENCE.html)
* [API Dock](http://apidock.com/) (Rails, Ruby, RSpec documentation) 
* [Railscasts](railscasts.com)
* [Rails Best Practices](http://rails-bestpractices.com/)
* [Factory Girl Documentation](http://rubydoc.info/gems/factory_girl/1.3.3/frames)

## TODOs

* Document spork install
* Add fiveruns dev toolbar
* Add rails optimization gem from rails lab vids


