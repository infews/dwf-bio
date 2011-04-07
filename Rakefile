require 'aws/s3'

desc "Build the website"
task :build => :clear do
  Dir.chdir('source') do
    system('frank export ../output')
  end
end

desc "Clear the output directory"
task :clear do
  puts "clearing output directory"
  FileUtils.rm_r('output') if File.exists?('output')
end

desc "Copy files to S3"
task :deploy => :build do

  AWS::S3::Base.establish_connection!(:access_key_id     => "0124C8E5VGPVEVHH0J02",
                                      :secret_access_key => "k/Ph58dw5FNwhKc6wKFNNKDYbGYQDGsWEZdAVoTY")

  buckets = AWS::S3::Service.buckets
  buckets.each {|b| b.delete_all if b.name == bucket_name}

  puts "...uploading new files to bucket #{bucket_name}"
  Dir.chdir('output') do
    site_files.each do |f|
      AWS::S3::S3Object.store(f, open(f), bucket_name, :access => :public_read)
    end
  end
end

desc "List all current buckets"
task :buckets do
  AWS::S3::Base.establish_connection!(:access_key_id     => "0124C8E5VGPVEVHH0J02",
                                      :secret_access_key => "k/Ph58dw5FNwhKc6wKFNNKDYbGYQDGsWEZdAVoTY")

  AWS::S3::Service.buckets.each {|b| puts b.name}
end

desc "Start up the Frank server"
task :up do
  Dir.chdir('source') do
    system("frank up")
  end
end



def bucket_name
  'dwf.bigpencil.net'
end

def site_files
  Dir.glob('**/*').collect { |f| f unless File.directory?(f) }.compact
end