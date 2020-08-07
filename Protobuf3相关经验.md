#Protobuf3
Protobuf3的优点
灰度升级、微服务有关系

## 官方说法 ##
protocol buffers 是一种语言无关、平台无关、可扩展的序列化结构数据的方法，它可用于（数据）通信协议、数据存储等。
Protocol Buffers 是一种灵活，高效，自动化机制的结构数据序列化方法－可类比 XML，但是比 XML 更小（3 ~ 10倍）、更快（20 ~ 100倍）、更为简单。你可以定义数据的结构，然后使用特殊生成的源代码轻松的在各种数据流中使用各种语言进行编写和读取结构数据。你甚至可以更新数据结构，而不破坏由旧数据结构编译的已部署程序。

## 特点 ##
1. 语言无关、平台无关。即 ProtoBuf 支持 Java、C++、Python 等多种语言，支持多个平台
2. 高效。即比 XML 更小（3 ~ 10倍）、更快（20 ~ 100倍）、更为简单
3. 扩展性、兼容性好。你可以更新数据结构，而不影响和破坏原有的旧程序

##项目中为什么使用


##1、安装方式
    sudo apt-get install autoconf automake libtool curl make g++ unzip
    git clone https://github.com/google/protobuf.git
    cd protobuf
    git submodule update --init --recursive
    ./autogen.sh
    ./configure
    make
    make check
    sudo make install
    sudo ldconfig # refresh shared library cache.
    
##2、 基本例子 ##
###2.1 Proto文件
    
    syntax = "proto3";//指定版本 使用protobuf3
    message Account 
    {
    	//账号
    	uint64 ID = 1;
    	//名字
    	string name = 2;
    	//密码
    	string password = 3;
    }

###2.2 编译Proto文件
    
    protoc --cpp_out=./ project.proto

参数解释
    
    需要生成什么语言的代码需要直接指定，例如C++
    protoc --cpp_out=./ project.proto
    protoc是工具名， 可以直接运行的命令。
    `--cpp_out是参数， 指定生成C++代码， =后面指定生成的目录。`
    project.proto是定义的文件。

###2.3 结果生成
    project.pb.h 和 project.pb.cc

###2.4 以单个字段为例子
      void clear_name();
      const std::string& name() const;
      void set_name(const std::string& value);
      void set_name(std::string&& value);
      void set_name(const char* value);
      void set_name(const char* value, size_t size);
      std::string* mutable_name();   //易错
      std::string* release_name();   //易错
      void set_allocated_name(std::string* name);
      private:
      const std::string& _internal_name() const;
      void _internal_set_name(const std::string& value);
      std::string* _internal_mutable_name();

###2.6 文件读取例子
address.proto

    syntax = "proto3";//指定版本 使用protobuf3
    package tutorial;
    message Persion 
    {
    	required string name = 1;
    	required int32 age = 2;
    }
    message AddressBook 
	{
    	repeated Persion persion = 1;
    }

write.cpp

    #include <iostream>
    #include <fstream>
    #include <string>
    #include "address.pb.h"
    using namespace std;
    
    void PromptForAddress(tutorial::Persion *persion) 
    {
    	cout << "Enter persion name:" << endl;
    	string name="zyc";
    	persion->set_name(name);

    	int age=100;
    	persion->set_age(age);
    }
    
    int main(int argc, char **argv) 
    {
    	//GOOGLE_PROTOBUF_VERIFY_VERSION;

    	if (argc != 2) 
    	{
      	  	cerr << "Usage: " << argv[0] << " ADDRESS_BOOL_FILE" << endl;
       	 	return -1;
    	}

    	tutorial::AddressBook address_book;
   	 	{
        	fstream input(argv[1], ios::in | ios::binary);
        	if (!input) 
        	{
            	cout << argv[1] << ": File not found. Creating a new file." << endl;
        	}
        	else if (!address_book.ParseFromIstream(&input)) 
        	{
            	cerr << "Filed to parse address book." << endl;
            	return -1;
        	}
    	}

    	// Add an address
    	PromptForAddress(address_book.add_persion());
    	{
       	 	fstream output(argv[1], ios::out | ios::trunc | ios::binary);
        	if (!address_book.SerializeToOstream(&output)) 
        	{
            	cerr << "Failed to write address book." << endl;
            	return -1;
        	}
    	}

    	// Optional: Delete all global objects allocated by libprotobuf.
    	//google::protobuf::ShutdownProtobufLibrary();

        return 0;
    }

read.cpp
    #include <iostream>
    #include <fstream>
    #include <string>
    #include "address.pb.h"
    
    using namespace std;
    
    void ListPeople(const tutorial::AddressBook& address_book) 
	{
    	for (int i = 0; i < address_book.persion_size(); i++) 
		{
    		const tutorial::Persion& persion = address_book.persion(i);
    
    		cout << persion.name() << " " << persion.age()<<" "<<persion.type() << endl;
    	}
    }
    
    int main(int argc, char **argv)
	{
    	//GOOGLE_PROTOBUF_VERIFY_VERSION;
    
    	if (argc != 2) 
		{
    		cerr << "Usage: " << argv[0] << " ADDRESS_BOOL_FILE" << endl;
    		return -1;
    	}
    
    	tutorial::AddressBook address_book;
    	{
    		fstream input(argv[1], ios::in | ios::binary);
    		if (!address_book.ParseFromIstream(&input)) 
			{
    			cerr << "Filed to parse address book." << endl;
    			return -1;
    		}
    		input.close();
    	}
   	    ListPeople(address_book);
    	// Optional: Delete all global objects allocated by libprotobuf.
    	//google::protobuf::ShutdownProtobufLibrary();
    
    	return 0;
    }


编译方式：



###2.5 序列化与反序列化

    
1、技术点
Demo

2、编码、序列化等

https://blog.csdn.net/tennysonsky/article/details/74096431?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.channel_param