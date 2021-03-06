require 'fileutils'

# Files and folders which shouldn't be copied over
$exclude = %w(. .. .git .gitignore Gemfile Gemfile.lock Rakefile README.md .sublime_packages)

# Files
def entries
  @files ||= Dir.entries( File.expand_path( '~/.dotfiles' ) ) - $exclude
end

desc 'Backup previous dotfiles.'
task :backup do
  dir = File.expand_path File.join( '~' , '.dotfiles-backup', Time.now.to_i.to_s )
  FileUtils.mkdir_p dir
  entries.each do | file |
    orig = File.expand_path "~/#{file}"
    FileUtils.cp_r orig, "#{dir}/#{file}", :verbose => true if File.exists? orig
  end
end

desc 'Update dotfiles repository.'
task :update do
  system 'git pull'
end

desc 'Run basic install tasks in order.'
task :install => %w(install:deps install:formulae install:copy install:post)

desc 'Run node related install tasks'
task :node => %w(install:nvm install:npm)

namespace :install do

  desc 'Check for and install required dependencies.'
  task :deps do
    # Homebrew
    puts 'Please install homebrew' and exit 1 unless system 'which brew'

    # Sublime Text
    puts 'Please install Sublime Text' and exit 1 unless system 'which sublime'

    unless system 'subl'
      system 'ln -s /Applications/Sublime\ Text\ 3.app/Contents/SharedSupport/bin/subl /usr/local/bin/sublime'
    end
  end

  desc 'Copy dotfiles over to home dir.'
  task :copy do
    entries.each do | file |
      FileUtils.cp_r file, File.expand_path( "~/#{file}" ), :verbose => true, :remove_destination => true
    end
  end

  desc 'Install required brew formulae'
  task :formulae do
    %w(bash-completion git rbenv ruby-build hub).each do |f|
      system "brew install #{f}"
    end

    # link up homebrew bash completion
    puts 'Link up homebrew bash completion'
    system 'ln -s "/usr/local/Library/Contributions/brew_bash_completion.sh" "/usr/local/etc/bash_completion.d"'
  end

  desc 'Install NVM'
  task :nvm do
    system 'curl https://raw.github.com/creationix/nvm/master/install.sh | sh'
  end

  desc 'Install required npm packages'
  task :npm do
    unless system 'which npm'
      system 'curl http://npmjs.org/install.sh | sh'
    end

    %w(grunt-cli coffee-script stylus).each do |f|
      system "npm install -g #{f}"
    end
  end

	desc 'Install ruby'
  task :ruby do
    puts 'Installing ruby 2.1.1 via rbenv install'
    system 'rbenv install 2.1.1'
    system 'rbenv global 2.1.1'
    system 'sudo gem install bundler'
    system 'rbenv rehash'
	end

  desc 'Run post-install tasks.'
  task :post do
		puts "Linking ST2 packages"

		package_source = '~/.dotfiles/.sublime_packages'
		package_target = '~/Library/Application\ Support/Sublime\ Text\ 3/Packages'

		system "rm -rf #{package_target}"
		system "ln -sF #{package_source} #{package_target}"

    puts "\n\n\n##################################################"
    puts "Don't forget to edit your git config: ~/.gitconfig"
  end

end
