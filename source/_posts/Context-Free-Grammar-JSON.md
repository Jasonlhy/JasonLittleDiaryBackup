title: 'Context-Free Grammar & JSON'
date: 2016-03-20 03:33:59
description:
categories: Note
tags:
- Compiler Principle
- JSON
- Formal Language
---

# JSON 和Context-free Grammar的關係 
Copy from [JSON.org](http://www.json.org)
<pre class="sample">
object
  {}
  { members }
members
  pair
  pair , members
pair
  string : value
array
  []
  [ elements ]
elements
  value 
  value , elements
value
  string
  number
  object
  array
  true
  false
  null
</pre>

JSON 可以由Context-free Grammar 所做成。當中的rule都是non-ambiguous，內裡的leftmost derivation, 都是deterministic (只有一個方法)。而且可以先做rule1 再做rule2。因此他是LL(1) parser, 可以用predictive parsing，用recursive decent method.

每次先看看一個character 然後決定走那條rule
下面是sample code,當中的members和elements 不用recursion那麼深... 可以用while 取代, syntax error detection 的做法參加了原作者, 一檢測到就throw exception



# Sample Code
{% codeblock lang:java %}
public class JSONValidator {
	private String jsonString;
	private int idx = -1; // next scan character index

	public JSONValidator(String jsonString) {
		this.jsonString = jsonString;
		this.idx = 0;
	}

	public char peek() {
		return jsonString.charAt(idx);
	}


	public void next(char expectedChar) {
		if (peek() != expectedChar) {
			// run time exceptions
			throw new JSONParseException("Expected " + expectedChar + " at " + idx + " but " + peek() + " is found");
		}

		idx++;
	}


	public void next() {
		idx++;
	}

	public void parse() {
		object();
	}

	public void object() {
		ws();
		next('{');
		ws();
		if (peek() == '}') {
			// System.out.println("Empty JSON Object");
			next('}');
		} else {
			members();
			ws();
			next('}');
		}
	}

	public void members() {
		ws();
		pair();
		ws();
		
		
		if (isEnd()){
			throw new JSONParseException("Unexpected ending of JSON Object, may be missing } ");
		}
		
		if (peek() == ',') {
			next(',');
			members();
		}
	}

	public void pair() {
		ws();
		String key = string();
		System.out.println("Key in json object: " + key);
		ws();
		next(':');
		ws();
		String value = value();
		System.out.println("Value in json object: " + value);
	}

	public String value() {
		ws();
		
		if (isEnd()){
			throw new JSONParseException("Expected a JSON value at " + idx);
		}
		
		char ch = peek();
		String returnStr = "";
		
		switch (ch){
		case '{' : 
			object();
			break;
		case '[' :
			array();
			break;
		case '"' :
			return string();
		case '-':
			return number();
		default:
			returnStr = (Character.isDigit(ch)) ? number() : word() + ""; 
		}
		
		return returnStr;
	}
	
	public String word() {
		char ch = peek();
		switch (ch) {
		case 't':
			next('t');
			next('r');
			next('u');
			next('e');
			return "true";
		case 'f':
			next('f');
			next('a');
			next('l');
			next('s');
			next('e');
			return "false";
		case 'n':
			next('n');
			next('u');
			next('l');
			next('l');
			return "null";
		default:
			throw new JSONParseException("Unknowned token " + ch + " at " + idx);
		}

	}

	public void array() {
		ws();
		next('[');
		ws();
		
		if (peek() == ']'){
			next(']'); // empty array;
		} else {
			elements();
			ws();
			next(']');
		}
	}

	public void elements() {
		ws();
		String value = value();
		System.out.println("value of array elements: " + value);
		ws();
		if (peek() == ','){
			next(',');
			elements();
		}
	}

	public boolean isEnd() {
		return idx >= jsonString.length();
	}

	/** skip white Space */
	public void ws() {
		while (!isEnd()) {
			if (Character.isWhitespace(peek())) {
				idx++;
			} else {
				break;
			}
		}
	}

	public String number() {
		ws();
		int initIdx = idx;
		
		String numberString = "";
		if (peek() == '-'){
			next('-');
			numberString += '-';
		}
		
		// digit before .
		while (!isEnd()) {
			char c = peek();
			if (Character.isDigit(c)) {
				numberString += c;
				idx++;
			} else {
				break;
			}
		}
		
		if (isEnd()){
			throw new JSONParseException("Unexpected number at the end");
		}
		
		// digit after .
		if (peek() == '.'){
			next('.');
			numberString += '.';
			
			while (!isEnd()) {
				char c = peek();
				if (Character.isDigit(c)) {
					numberString += c;
					idx++;
				} else {
					break;
				}
			}
		}

		// integer or double
		try {
			System.out.println("Parse as Int for " + numberString);
			return Integer.parseInt(numberString) + "";
		} catch (NumberFormatException ex){
			try {
				System.out.println("Parse as double for " + numberString);
				return Double.parseDouble(numberString) + "";
			} catch (NumberFormatException ex2){
				throw new JSONParseException("Expected number at " + initIdx + " but it is not found");
			}
		}
		
	}

	public int nextInt() {
		ws();

		String intStr = "";
		while (!isEnd()) {
			char c = peek();
			if (Character.isDigit(c)) {
				intStr += c;
				idx++;
			} else {
				break;
			}
		}

		return Integer.parseInt(intStr);
	}

	/* next string without "" */
	public String string() {
		ws();

		String str = null;
		boolean scanning = false;

		char c = peek();
		if (c == '"') {
			scanning = true;
			str = "";
			next();
		}

		while (!isEnd() && scanning) {
			c = peek();

			if (c == '"') {
				scanning = false;
				next();
			} else {
				str += c;
				next();
			}
		}

		return str;
	}

	
}
{% endcodeblock %}
  