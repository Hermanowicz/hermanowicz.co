---
title: 'Ruby basics'
description: 'An overview of ruby lang basics'
pubDate: 'Jun 14 2024'
heroImage: '/blog-placeholder-4.jpg'
tags: ["ruby", "code", "basics"]
---

If you're a sysadmin looking to automate your server management tasks, Ruby might be the perfect language for you. With its simple syntax and powerful libraries, Ruby can help you write scripts and tools that make managing servers and AWS much easier.

## Introduction

Ruby is an interpreted, high-level, general-purpose programming language. It was designed and developed in the mid-1990s by Yukihiro "Matz" Matsumoto in Japan. Ruby is dynamically typed and uses garbage collection. It supports multiple programming paradigms, including procedural, object-oriented, and functional programming.

The language is known for its simplicity and elegance, which makes it easy to learn and use. It's also highly extensible, with a vast library of third-party libraries and gems that can be easily integrated into your code.

## Basics of Ruby

Ruby has a simple syntax that is easy to learn. Here are some of the basics:

### Types

Ruby has a number of built-in types, including integers, floats, decimals, and big numbers. You can also create your own types using classes.

```ruby
# Integers
a, b = 1, 2

# Floats
c, d = 1.5, 2.5

# Decimals those require pull of std-lib 'bigdecimal'
e = BigDecimal('0.9')
f = BigDecimal('0.1')
sum = e + f # = 1 ps. use method to_f to move from sientific notation

# Big numbers can use '_' to amke them more readible
larg_num = 1_000_000_000_000
```

### Logic

Ruby has a number of logical operators, including `if`, `case`, `&&`, and `||`. These can be used to control the flow of your code and perform conditional operations.

```ruby
flag = true
if flag
	puts "hello, world"
end

# -----------------------
flag = false
if flag
	puts "hello when true"
else
	puts "hello when false"
end

# -----------------------
flag = false
if is_correct_file?
	puts "hello when true"
elsif is_folder?
	puts
else
	puts "hello when false"
end

# -----------------------
case x
when 1..5
	puts "one to five"
when 6
	puts "six"
when "foo"
	puts "prints foo"
when String
	puts "input was string"
else
	"I don't know"
end

# -----------------------
if first_check? && second_check?
	# ..... AND
	puts "job done"
end

# -----------------------
if first_check? || second_check?
	# ..... OR
	puts "job done"
end
```

### Loops

Ruby has a number of loop constructs, including `.times`, `.each`, `.upto`, and `for`. These can be used to perform repetitive tasks or iterate over collections.

```ruby
# -----------------
# .times

5.times do
	puts "hello"
end

# -----------------
# .each

[1, 2, 3].each do |i|
	puts i
end

# -----------------
# .upto

1.upto(5) do |i|
	puts i
end

# -----------------
# for i in []

for i in [1, 2, 3]
	puts i
end
```

### File Operations

Ruby has built-in support for file and directory operations, including reading and writing files, creating directories, and listing directory contents.

```ruby
# Read a file
file = File.open("example.txt", "r")
puts file.read
file.close

# Write to a file
File.open("example.txt", "w") do |file|
	file.write("hello, world")
end
```

### Modules and Classes

Ruby has a powerful module and class system that makes it easy to organise your code and create reusable components.

```ruby
# Define a module
module MyModule
	def say_hello
		puts "hello from MyModule"
	end
end

# Include a module in a class
class MyClass
	include MyModule
end

# Create a new object of the class
obj = MyClass.new

# Call a method from the module
obj.say_hello
```

## Ruby Dir Class: A Guide with Examples

Ruby's `Dir` class is a powerful tool for managing directories in your code. In this guide, we'll explore what the `Dir` class is, how to use it, and some examples of its functionality.

## What is the Dir Class?

The `Dir` class is a built-in class in Ruby that provides a way to interact with directories in your file system. It allows you to perform various operations such as creating new directories, deleting directories, listing files in a directory, and more.

## Basic Usage

Before we dive into some examples, let's start with some basic usage of the `Dir` class.

### Listing Files in a Directory

To list all the files in a directory, you can use the `Dir.entries` method. This method returns an array of all the files and directories in the specified directory.

```ruby
# List all files in the current directory
Dir.entries(".").each do |file|
  puts file
end

# Output:
# .
# ..
# file1.txt
# file2.rb
```

### Creating a New Directory

To create a new directory, you can use the `Dir.mkdir` method. This method takes a string argument that specifies the name of the directory you want to create.

```ruby
# Create a new directory called "mydir"
Dir.mkdir("mydir")

# Check that the directory was created
puts Dir.exist?("mydir")

# Output: true
```

### Deleting a Directory

To delete a directory, you can use the `Dir.delete` method. This method takes a string argument that specifies the name of the directory you want to delete.

```ruby
# Delete the "mydir" directory
Dir.delete("mydir")

# Check that the directory was deleted
puts Dir.exist?("mydir")

# Output: false
```

## Advanced Usage

Now that we've covered some basic usage of the `Dir` class, let's dive into some more advanced examples.

### Recursively Listing Files in a Directory

To list all the files in a directory and its subdirectories, you can use the `Dir.glob` method. This method takes a string argument that specifies the path to the directory you want to list files in, plus a pattern that specifies the files you want to include.

```ruby
# List all .txt files in the current directory and its subdirectories
Dir.glob("**/*.txt").each do |file|
  puts file
end

# Output:
# file1.txt
# subdir/file3.txt
```

### Checking if a Directory is Empty

To check if a directory is empty, you can use the `Dir.empty?` method. This method takes a string argument that specifies the path to the directory you want to check.

```ruby
# Create an empty directory
Dir.mkdir("emptydir")

# Check if the directory is empty
puts Dir.empty?("emptydir")

# Output: true
```

### Changing the Current Working Directory

To change the current working directory, you can use the `Dir.chdir` method. This method takes a string argument that specifies the path to the directory you want to change to.

```ruby
# Change the current working directory to "mydir"
Dir.chdir("mydir")

# Check that the current working directory was changed
puts Dir.pwd

# Output: /path/to/mydir

```

### Creating a Temp Directory

To create a temporary directory, you can use the `Dir.mktmpdir` method. This method creates a new temporary directory with a unique name and returns the path to the directory.

```ruby
require 'tmpdir'

# Create a temporary directory
tempdir = Dir.mktmpdir

# Output the path to the temporary directory
puts tempdir

# Output: /var/folders/9p/gxj7x00n4fz00000gn/T/d20190703-2188-1e0zqf2
```

## Ruby for Server Automation

Ruby's simplicity and flexibility make it a great choice for server automation tasks. Here are some examples of how you can use Ruby to automate your server management:

### Automating Server Configuration

You can use Ruby to write scripts that automate server configuration tasks, such as installing software, configuring services, and setting up users and permissions.

```ruby
# Install a package
system("apt-get install vim")
# OR ----------------
`apt-get install vim`

# Configure a service
system("systemctl enable nginx")
# OR ----------------
`systemctl enable nginx`

# Create a user
system("useradd -m -s /bin/bash john")
# OR ----------------
`useradd -m -s /bin/bash john`

# Set permissions on a file
File.chmod(0644, "example.txt")
# OR ----------------
`chmod 0644 example.txt`
```

### Automating AWS Management

Ruby has a number of libraries and tools that make it easy to automate AWS management tasks, such as creating EC2 instances, managing S3 buckets, and configuring load balancers.

```ruby
# Create an EC2 instance
require 'aws-sdk-ec2'

ec2 = Aws::EC2::Resource.new(region: 'us-east-1')

instance = ec2.create_instances({
  image_id: 'ami-0c55b159cbfafe1f0',
  min_count: 1,
  max_count: 1,
  instance_type: 't2.micro',
  key_name: 'my-key-pair'
})

# Create an S3 bucket
require 'aws-sdk-s3'

s3 = Aws::S3::Client.new(region: 'us-east-1')

resp = s3.create_bucket({
  acl: "public-read",
  bucket: "my-bucket",
  create_bucket_configuration: {
    location_constraint: "us-east-1"
  }
})

# Configure a load balancer
require 'aws-sdk-elasticloadbalancing'

elb = Aws::ElasticLoadBalancing::Client.new(region: 'us-east-1')

resp = elb.create_load_balancer({
  load_balancer_name: 'my-load-balancer',
  listeners: [
    {
      protocol: 'HTTP',
      load_balancer_port: 80,
      instance_protocol: 'HTTP',
      instance_port: 80
    }
  ],
  availability_zones: ['us-east-1a'],
  security_groups: ['my-security-group'],
  subnets: ['subnet-12345678']
})
```

### Monitoring and Logging

With Ruby, you can write scripts that monitor server performance, check log files for errors, and send alerts when issues are detected.

```ruby
# Monitor server performance
require 'sys/proctable'

procs = Sys::ProcTable.ps

procs.each do |proc|
	puts "#{proc.pid} #{proc.comm}"
end

# Check log files for errors
File.open("example.log", "r") do |file|
	file.each_line do |line|
		if line =~ /error/
			puts line
		end
	end
end

# Send alerts when issues are detected
require 'net/smtp'

message = <<MESSAGE_END
From: Admin <admin@example.com>
To: User <user@example.com>
Subject: Server Error

There was an error on the server.

MESSAGE_END

Net::SMTP.start('smtp.example.com') do |smtp|
	smtp.send_message message, 'admin@example.com', 'user@example.com'
end
# -----------------------
Net::SMTP.start('smtp.example.com', 587, 'mydomain.com', 'user_name', 'password') do |smtp|
	smtp.send_message message, 'admin@example.com', 'user@example.com'
end
```

## Conclusion

If you're a sysadmin looking to automate your server management tasks, Ruby is definitely worth considering. With its simple syntax and powerful libraries, Ruby can help you write scripts and tools that make managing servers and AWS much easier.

While this article provides a brief introduction to Ruby for server automation, there is much more to learn. The best way to get started is to experiment with the language and explore the vast library of third-party gems and tools available. With practice, you'll be able to write powerful scripts and tools that make your server management tasks a breeze.# Automating Servers and AWS with Ruby for Sysadmins

If you're a sysadmin looking to automate your server management tasks, Ruby might be the perfect language for you. With its simple syntax and powerful libraries, Ruby can help you write scripts and tools that make managing servers and AWS much easier.
