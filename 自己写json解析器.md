## 自己动手写一个json解析器

在网络编程中，我们经常用到json作为网络传输的对象，因为json更加精简。所以我打算自己动手写一个json解析器，因为这样可以提高自己动手写代码的逻辑能力，后期重构代码时加入设计模式，可以让自己编码能力更上一层楼。

我在造轮子，但我希望自己的轮子足够简单，可以让更多喜欢造轮子的朋友拿我的轮子作为借鉴。



Jsonparser的思路
readObject
通过读取第一个Token字符，是空格的话跳过去继续读取下一个。

private JsonException exception( String str ){
	return new JsonException( str );
}

String readString(){
	StringBuilder sb = new StringBuilder();
	int c = 0;
	while( true ){
		c = reder.read();
		if( c == '\"' ){
			break;
		}
		sb.append( c );
	}
	return sb.toString();
}

Map<String,Object> readMap(){
	List<String> keys = new LinkedList<>();
	List<Object> values = new LinkedList<>();
	boolean next = false;
	while( true ){
		int nextToken = nextToken();
		if( nextToken == -1 ){
			throw exception( "Json字符串未正常关闭" );
		}
		if( nextToken == '}' ){
			break;
		}
		if( next ){
			if( nextToken != ',' ){
				throw exception( "Json字符串格式错误:此处需要有逗号了" );
			}
			next = false;
			continue;
		}
		if( nextToken == ',' ){
			throw exception( "Json字符串格式错误:此处不应该有逗号" );
		}
		if( nextToken != '\"' ){
			throw exception( "Json字符串格式错误:Map格式需要key值" );
		}
		String key = readString();
		nextToken = nextToken();
		if( nextToken != ':' ){
			throw exception( "Json字符串格式错误:Map格式key后面需要:" );
		}
		Object value = readObject();
		keys.add( key );
		values.add( value );
		next = true;
	}
	Map<String,Object> map = new HashMap<>(keys.size());
	int i = 0;
	for( String key : keys  ){
		Object value = values.get(i);
		map.put( key, value );
		++i;
	}
	return map;
}

List<Object> readList(){
	List<Object> ans = new LinkedList<>();
	boolean next = false;
	while( true ){
		int nextToken = nextToken();
		if( nextToken == -1 ){
			throw exception("Json字符串格式不正确,json未正确关闭");
		}
		if( nextToken == ']' ){
			break;
		}
		if( next ){
			if( nextToken != ',' ){
				throw exception( "Json字符串格式错误:此处需要有逗号了" );
			}
			next = false;
			continue;
		}
		if( nextToken == ',' ){
			throw exception( "Json字符串格式错误:此处不应该有逗号" );
		}
		next  = true;
		if( nextToken == '\"' ){
			ans.add( readString() );
			continue;
		}
		if( nextToken == '{' ){
			ans.add( readMap() );
			continue;
		}
		if( nextToken == '[' ){
			ans.add( readList() );
			continue;
		}
		if( nextToken == 'n' ){
			ans.add( readNull() );
			continue;
		}
		//数字
	}
	return ans;
}

int readInt( int start ){
	
	return 10;
}

Object readNull(){
	int c = reader.read();
	if( c != 'u' ){
		throw new JsonException("Json字符串格式不正确");
	}
	c = reader.read();
	if( c != 'l' ){
		throw new JsonException("Json字符串格式不正确");
	}
	c = reader.read();
	if( c != 'l' ){
		throw new JsonException("Json字符串格式不正确");
	}
	return null;
}

int nextToken(){
	int tokenType = 0;
	do{
		tokenType = reader.read();
	}while( tokenType == ' ' || tokenType == '\r' || tokenType == '\n' );
	return tokenType;
}

Object readObject(){
	int tokenType = nextToken();
	if( toeknType == -1 ){
		//这里有两种情况要考虑，一种是被read调用时，空字符串返回null，正确
		//如果被其他的方法调用，那么我们可以断定json格式不正确，因为他没正确关闭，
		//应该抛出异常，但是没有，因为其他方法会判断的
		return null;
	}
	if( tokenType == '{' ){
		return readMap();
	}
	if( tokenType == '[' ){
		return readlist();
	}
	if( tokenType == 'n' ){
		return readNull();
	}
	if( token == '\"' ){
		return readString();
	}
	if( tokenType == ',' ){
		throw new JsonException("Json字符串格式不正确");
	}
	throw new JsonException("Json字符串格式不正确");
}

Object read(){
	Object obj = readObject();
	int tokenType = nextToken();
	if( tokenType != -1 ){
		throw new JsonException("Json字符串格式不正确:未关闭");
	}
	return obj;
}

