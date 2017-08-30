---
layout: post
section-type: post
title: From Certificate to Environment Variable
category: ruby code
tags: [ 'paypal, app, web development, SSL, ENV, environment variable, figaro, application.yml, Rails, Ruby' ]
---

G'day!

After YAML ruined my day, I decided I would create a means for others to avoid such pain.  Yesterday I spent some time
creating a ruby class that will take an SSL certificate and turn it into an environment variable. I use the 
[figaro gem ](https://github.com/laserlemon/figaro) to manage the secret keys in my Rails apps so its designed to work with that
but could be configured simply enough to append the key it generates wherever you like. Just remember that you should not 
publish your keys on [github](https://github.com) (or similar) so wherever you decide to append the output of this code, 
make sure it's included in your .gitignore file. I created this code to live in a certs directory (rails root) that holds
my SSL certificates so it uses specific directory changes to find /railsRoot/config/application.yml in the app.
 
Hope this little snippet of code saves a few hours of frustration. Any suggestions to make it better are always welcome. 

Happy coding,

Brendan



###[certificate.rb](https://gist.github.com/titanium-cranium/b96c751f15dd84fc40e8c4306ada565d)

    class Certificate
    
      def initialize(keyname, file)
        if file.nil?
          puts "Filename required"
          return
        end
    
        if keyname == "" || keyname.nil?
          puts "Keyname required"
          return
        end
        @key = ""
        @cert = File.open(file, "r")
        @keyname = keyname.upcase
    
      end
    
    
      def create_key
        @key = @keyname + ": \""
          @cert.each_line do |line|
            puts line.strip
            @key = @key + line.strip + "\\n"
          end
        @key = @key + "\""
        puts @key
      end
    
      def append_key
        Dir.chdir("..") do
          Dir.chdir("./config") do
            f = File.open("application.yml", "a")
            f << "\n"
            f << @key
          end
        end
      end
    
    end



