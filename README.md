# Serverspec

RSpec tests for your provisioned servers

----

## Installation

Add this line to your application's Gemfile:

    gem 'serverspec'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install serverspec

----

## Usage

```
$ serverspec-init
 + spec/
 + spec/www.example.jp/
 + spec/www.example.jp/httpd_spec.rb
 + spec/spec_helper.rb
 + Rakefile
```
spec/www.example.jp/httpd_spec.rb is a sample spec file and its content is like this.

```ruby
require 'spec_helper'

describe 'httpd', :os => :redhat do
  it { should be_installed }
  it { should be_enabled   }
  it { should be_running   }
end

describe 'port 80', :os => :redhat do
  it { should be_listening }
end

describe '/etc/httpd/conf/httpd.conf', :os => :redhat do
  it { should be_file }
  it { should contain "ServerName www.example.jp" }
end
```

You can write spec for testing provisioned servers like this.

You should create ~/.ssh/config like this before running tests.

```
Host *.example.jp
   User root
   IdentityFile ~/.ssh/id_rsa
```

Run tests.

```
$ rake spec
```
----
## Multi OS support

Serverspec is supporting Red Hat (and its clone) and Debian (and its clone) now.

If your target host's OS is Debian, you can write the spec like this.

```ruby
require 'spec_helper'

describe 'httpd', :os => :debian do
  it { should be_installed }
  it { should be_enabled   }
  it { should be_running   }
end

describe 'port 80', :os => :debian do
  it { should be_listening }
end

describe '/etc/httpd/conf/httpd.conf', :os => :debian do
  it { should be_file }
  it { should contain "ServerName www.example.jp" }
end
```

Or like this.

```ruby
require 'spec_helper'

describe 'www.example.jp', :os => :debian do
  it do
    'httpd'.should be_installed
  end

  it do
    'httpd'.should be_enabled
  end

  it do
    'httpd'.should be_running
  end

  it do
    'port 80'.should be_listening
  end

  conf = '/etc/httpd/conf/httpd.conf'

  it do
    conf.should be_file
  end

  it do
    conf.should contain "ServerName www.example.jp"
  end
end
```

Or modify generated spec/spec_helper.rb like this

```ruby
require 'serverspec'
require 'pathname'

RSpec.configure do |c|
  c.include(Serverspec::DebianHelper)
  c.before do
    c.host = File.basename(Pathname.new(example.metadata[:location]).dirname)
  end
end
```

And write the spec like this.

```ruby
require 'spec_helper'

describe 'httpd' do
  it { should be_installed }
  it { should be_enabled   }
  it { should be_running   }
end

describe 'port 80' do
  it { should be_listening }
end

describe '/etc/httpd/conf/httpd.conf' do
  it { should be_file }
  it { should contain "ServerName www.example.jp" }
end
```

Choose any style you like.


----

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
