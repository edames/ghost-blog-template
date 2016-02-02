namespace :databag do
  task :check_secret do
    raise 'missing encryption key - ./.chef/data_bag_secret.pem' unless File.exists?('.chef/data_bag_secret.pem')
  end

  namespace :upload do
    desc "Upload all Data Bags"
    task :all => :check_secret do
      sh 'knife data bag from file -a'
    end
  end # namespace - databags:upload
end # namespace - databags

task :lint do
  sh 'foodcritic -f any ./cookbooks'
end

# there is a bug in aescrypt that requires
# we manually pull in base64
require 'base64'
require 'aescrypt'

namespace :keys do
  desc "encrypts the needed keys for saving in to source"
  task :encrypt do
    passphrase = ENV['KEYS_PASSPHRASE']
    raise "\nMissing ENV['KEYS_PASSPHRASE'] environment variable\n" unless passphrase
    files = ['.chef/deployuser.pem', '.chef/data_bag_secret.pem']
    files.each do |file|
      File.open("#{file}.enc", 'w') { |fh|
        puts "encrypting #{file} as #{file}.enc"
        fh.print(AESCrypt.encrypt(File.read(file), passphrase))
      }
    end
  end

  desc "decrypts the needed keys for pushing to the chef server"
  task :decrypt do
    passphrase = ENV['KEYS_PASSPHRASE']
    raise "\nMissing ENV['KEYS_PASSPHRASE'] environment variable\n" unless passphrase
    files = ['.chef/deployuser.pem', '.chef/data_bag_secret.pem']
    files.each do |file|
      File.open(file, 'w') { |fh|
        puts "decrypting #{file}.enc as #{file}"
        fh.puts(AESCrypt.decrypt(File.read("#{file}.enc"), passphrase))
      }
    end
  end
end # namespace - keys
