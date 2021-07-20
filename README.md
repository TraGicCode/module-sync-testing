# Perform a 1 time sync of file

1. Add file as unmanaged to `.sync.yml`
2. Create template file in moduleroot
3. Run msync.  It will create pr with file and update to .sync.yml.  Future sync's will ignore the file.


# Merge arrays

To merge arrays you must do this manually using some ruby code.  If you declare the array in the .sync.yml it will overwrite the WHOLE array in your config_defaults.yml


config_defaults.yml

```
README.md:
  my_array:
    - item1
    - item2
```

.sync.yml

```
README.md:
  my_array:
    - item3
```

my_array = [ 'item3' ]

To merge arrays you need to come up with 2 keys and then merge them in the code based on your requirements EX: GemFile required and optional arrays

# Merge hashes

Hashes are merged by default which is how the whole .sync.yml and config_defaults.yml works

config_defaults.yml
```

```

.sync.yml
```

```


# Define helper functions at the top of files

```
  
<%
def requires(item)
  if item.is_a? String
    line = "require '#{item}'"
  elsif item.is_a? Hash
    line = "require '#{item['require']}'" unless item['require'].nil?
    line = "#{line} if #{item['conditional']}" unless item['require'].nil? and item['conditional'].nil?
  end
  line
end
-%>
# frozen_string_literal: true

require 'bundler'
require 'puppet_litmus/rake_tasks' if Bundler.rubygems.find_name('puppet_litmus').any?
require 'puppetlabs_spec_helper/rake_tasks'
require 'puppet-syntax/tasks/puppet-syntax'
require 'puppet_blacksmith/rake_tasks' if Bundler.rubygems.find_name('puppet-blacksmith').any?
require 'github_changelog_generator/task' if Bundler.rubygems.find_name('github_changelog_generator').any?
require 'puppet-strings/tasks' if Bundler.rubygems.find_name('puppet-strings').any?
<% if ! @configs['requires'].nil? -%>
<%   @configs['requires'].each do |item| -%>
<%= requires(item) %>
<%   end -%>
<% end -%>
<% if ! @configs['imports'].nil? %>
<%   @configs['imports'].each do |item| -%>
import '<%= item %>'
<%   end -%>
<% end -%>

```