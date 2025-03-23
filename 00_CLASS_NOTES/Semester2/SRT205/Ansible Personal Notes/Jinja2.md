---
tags:
  - ansible
  - jinja2
aliases: 
creation_date: 2025-03-22 09:30
last_updated: 2025-03-23 09:49
---
# Jinja2
### About
- purpose:
	- templating engine 
- special placeholders in the template and then data is passed to it to render the final document 
- related to [[SRT205_Lec8]] 

### What is a Jinja template?
- a jinja template is a simple text file 
	- does NOT require a specific extension 
	- though with Ansible .j2 is the convention 
		- easy identification and management
- jinja can generate any text-based format 
	- HTML, XML, CSV, LaTeX etc.. 
- a template contains variables and/or expressions 
	- these gets replaced with values when the template is rendered 
- template tags
	- control logic of the template 





# Template Syntax
---
### Basic Template Syntax
- heavily inspired by Django and Python 
- the default Jinja delimiters are:
	- `{% ... %}` == statement
	- `{{ ... }}` == expression 
		- to print to the template output
	- `{# ... #}` == comment 


>[!note]+
>The double-curly braces are the print statement, and are *not* part of the variable
>- therefore, if accessing variables inside tags, don't put the braces around them


---


### Variables
- template variables are defined by the *context dictionary* passed to the template 
	- variables may have attributes / elements that can be access 
		- greatly depends on the source 
- variables are access using dot (.) notion or subscript syntax (\[ ])
	- ex:
		- `{{ foo.bar }}`
		- `{{ foo['bar'] }}`context dictionary



>[!question]+ What is a 'Context Dictionary'?
>The variables (key-value pairs) that are available to Jinja2 when the template is rendered 
>
>A *dictionary* is composed of:
>- variables defined in [[Ansible Playbook|playbook]] 
>- variables from [[Ansible Inventory|inventory]] 
>- [[Ansible Facts|Facts]] collected from hosts
>- extra vars passed at runtime 
>- [[Ansible Roles|role]] defaults etc.. 
>




---


### Filters
- https://jinja.palletsprojects.com/en/stable/templates/#list-of-builtin-filters
- variables can be modified by 'filters' 
- filters are separated from variables by the pipe `|` symbol 
- can chain multiple filters together
	- output of one filter is applied to the next from left to right 
	- ex: `{{ name|striptags|title }}` 
- some filters might have optional arguments in parentheses
	- ex: `{{ listx|join(', ') }}`
		- will join a list with commas


##### Common Filters
- from [[SRT205_Lec8]] slides
###### `default`: provide a default value if variable is undefined:
```jinja2
{{ var | default('default_value') }}
```

Example from [[SRT205_Lec8]]
```jinja2
log_path = {{ logs_path | default('/var/log/nginx') }}
```
- if no log path is specified, will default to /var/log/nginx

###### `upper`: convert text to uppercase
```jinja2
{{ hostname | upper }}
```

###### `replace`: replace parts of a string
```jinja2
{{ path | replace("/", "-") }}
```

###### `join`: join list into a string
```jinja2
{{ list | join(",") }}
```


>[!note]- Can apply filters to a *block* of template data 
>If you need to filter more than a single variable
>Syntax:
>```jinja2
>{% filter upper %}
>    This text becomes uppercase
>{% endfilter %}
>```



---


### Tests 
- List of built in tests:
	- https://jinja.palletsprojects.com/en/stable/templates/#builtin-tests
- purpose:
	- test a variable against a common expression 
- use the `is` keyword plus the name of the test after the variable 
	- ex: `name is defined`
		- checks if the `name` variable has been defined 
- tests can accept arguments 
	- if a test accepts 1 argument, can exclude parentheses 
		- ex: 
			- `{% if loop.index is divisibleby 3 %}`
			- `{% if loop.index is divisibleby(3) %}`
			- both are valid




---


### Whitespace Control 
- in default configuration:
	- a single newline is stripped if present 
	- other whitespace is returned unchanged 
		- ex: space, tabs, newlines etc..
- `trim_blocks`
	- the first newline after a template tag is removed automatically 
- `lstrip_block` 
	- can set this option to strip tabs and spaces 
- NOTE:
	- with both `trim_blocks` and `lstrip_block` disabled, lots of white space surrounding rendered variables
		- without, it is more compact 



---


### Escaping 
- when Jinja ignores parts that would otherwise be handled as variables or blocks 
	- ex: by default, cannot render {{ }} because would be interpreted as a variable 
- can surround by `{% raw %}` block 
	- ex: 

```jinja2
{% raw %}
    <ul>
    {% for item in seq %}
        <li>{{ item }}</li>
    {% endfor %}
    </ul>
{% endraw %}
```



---


# Template Inheritance 
---
>[!important]-
>Template inheritance is the 'most powerful part' of Jinja (according to documentation)  
>- BUT not important for what we are doing

### About
- template inheritance allows you to build a base 'skeleton' template that contains all common elements of your site and define 'blocks' that child templates can override 


## Base Template 
- 


---



# Control Structures
---
- default syntax for control structures: `{% ... %}`
- https://jinja.palletsprojects.com/en/stable/templates/#list-of-control-structures 



### Conditionals `if`
- `if` and `elif` are available 
- can use inline conditionals 
- can use conditionals for loop filtering as well 

Basic `if` syntax:
```jinja2
{% if users %}
<ul>
{% for user in users %}
    <li>{{ user.username|e }}</li>
{% endfor %}
</ul>
{% endif %}
```

Basic `elif` and `else` syntax:
```jinja2
{% if kenny.sick %}
    Kenny is sick.
{% elif kenny.dead %}
    You killed Kenny!  You bastard!!!
{% else %}
    Kenny looks okay --- so far
{% endif %}
```

##### Inline conditionals 
- can use inline `if` expressions 
- general syntax:
	- `<do something> if <something is true> else <do something else>`
		- // `else` is optional 




###### Conditional for loop filtering
```jinja2
{% for user in users if not user.hidden %}
    <li>{{ user.username|e }}</li>
{% endfor %}
```


---


### For loop

Basic for loop syntax: 
```jinja2
<h1>Members</h1>
<ul>
{% for user in users %}
  <li>{{ user.username|e }}</li>
{% endfor %}
</ul>
```


###### Example: from [[SRT205_Lec8]]
```jinja2
upstream backend {
	{% for server in backend_servers %}
	server {{ server }};
	{% endfor %}
}
```



---
















>[!note]+ Jina2 and [[Cross Site Scripting|XSS]]
>If `autoescape` is set to False, an attacker could inject malicious code that is rendered by Jinja.
>
>There are 2 ways to mitigate XSS attacks 
>1. always validate user input and reject malicious / dangerous code
>2. escape values when rendering HTML
>	3. purpose of the `autoescape: True` parameter 
>	4. option to apply auto-escaping based on file extension if desired
>
>see: https://www.codiga.io/blog/python-jinja2-autoescape/














# References
1. 