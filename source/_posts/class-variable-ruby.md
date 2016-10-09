---
date: 2013-08-03 18:32:55
tags:
title: Ruby中的类变量
---

今天在看Programming Ruby的时候学到了Ruby的类变量，又在网上搜了一些资料，总结一下：  
Ruby的类变量以2个@@符号来开头，例如@@count。类变量被类的所有对象共同拥有和共享，与实例变量不同，类变量在定义时候必须初始化，
借用书中的例子

```ruby
class Song
    #使用前初始化
    @@plays = 0

    attr_accessor :name, :artist, :duration
    
    def initialize(name, artist, duration)
    	@name = name
    	@artist = artist
    	@duration = duration
    	@play = 0
    end
    
    def play
    	@play += 1
    	@@play += 1
    	puts "This song #{@play} plays. Total play #{@@play} songs"
    end
end

s1 = Song.new("abc", "Rock", 350)
s2 = Song.new("def", "Pop", 430)

s1.play
s2.play

s3.play
s4.play
```

输出可以看到，类变量是被类的所有对象共享。

```ruby
This song: 1. Total play 1 songs.
This song: 1. Total play 2 songs.
This song: 2. Total play 3 songs.
This song: 2. Total play 4 songs.
```

子类成员可以访问和修改父类的类变量，看看下面的例子
```ruby
class Father
end

class Child1 < Father
    @@var = 222
    
    def self.var
    	puts @@var
    end
end

class Child2 < Father
    @@var = 333
    
    def self.var
    	puts @@var
    end
end

Child1.var  --> var in Child1
Child2.var  --> var in Child2
```

如果类变量不是从父类继承过来，子类中各自定义的类变量不会受影响

```ruby
class Father
    @@var = 111
    
    def self.var
    	puts @@var
    end
end
class Child1 < Father
    @@var = 222
    
    def self.var
    	puts @@var
    end
end

class Child2 < Father
    @@var = 333
    
    def self.var
    	puts @@var
    end
end
Father.var  --> var in Child2
Child1.var  --> var in Child2
Child2.var  --> var in Child2
```

如果不同子类中的类变量是从父类继承过来的，那么如果这个类变量被其中某个类中的方法修改后，会对所有类都生效。

```ruby
class Father
    @@var = 123
    
    def self.var
    	puts @@var
    end
end


class Child2 < Father
    @@var = 888
    def self.var
       puts @@var
    end

    def Child2.mod
       @@var = 999
    end
end

class Child1 < Father
    @@var = 234
    def self.var
	puts @@var
    end
end

Father.var  -->  234
Child1.var  -->  234
Child2.var  -->  234
Child2.mod  -->  234

Father.var  -->  999
Child1.var  -->  999
Child2.var  -->  999
```
