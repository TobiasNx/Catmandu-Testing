This cheat sheet summarizes the [[fix language]]. For more on the `marc_*` methods, see [MARC mapping rules](https://github.com/LibreCat/Catmandu-MARC/wiki/Mapping-rules).

```bash
# Fixes clean your data. As input you get a Perl HASH. Each fix function is a command
# to transform the Perl HASH. Some fixes such as marc_map contain logic to transform
# complex data structures such as MARC.
set_field("my.name","patrick")             <data>+<constant> # { my => { name => 'Patrick'} }
add_field("my.name2","nicolas")            <data>+<constant>
move_field("my.name","your.name")          <data source: name: > (löscht aber ursprünglichen Pfad)
copy_field("your.name","your.name2")       <data source: name: > (behält ursprünglichen Pfad bei)
remove_field("your.name")                  -- # keine Entsprechung in morph löscht Feld
# Replace in all the field names in 'foo' all dots into underscores
rename(foo,"\.","_")                       -- # keine Entsprechung in morph evtl <switch-name-value> + <regexp> + <switch-name-value>

set_array("foo")                           <entity name: "example[]"># Create an empty array foo => []
set_array("foo","a","b","c")                -- # keine Entsprechung in morph <entity name: "example[]"> + Werte # Create an array with three values foo => ['a','b','c']
set_hash("foo")                            <entity name: "example"> # Create an empty hash foo => {}
set_hash("foo",a: b,c: d)                  -- # keine Entsprechung in morph <entity name: "example[]"> + Unterfelder # Create an hash with two values foo => { a => 'b' , c => 'd' }

array("foo")                               -- # keine Entsprechung in morph # Create an array from a hash :
                                           # foo => {"name":"value"} => [ "name" , "value" ]
hash("foo")                                -- # keine Entsprechung in morph # Create a hash from an array
                                           # foo => [ "name" , "value" ] => {"name":"value"}

assoc(fields, pairs.*.key, pairs.*.val)    -- # keine Entsprechung in morph # Associate two values as a hash key and value
                                           # {pairs => [{key => 'year', val => 2009}, {key => 'subject', val => 'Perl'}]}
                                           # {fields => {subject => 'Perl', year => 2009}, pairs => [...]}

upcase("title")                            <case to="upper"> # marc -> MARC
downcase("title")                          <case to="lower"># MARC -> marc
capitalize("my.deeply.nested.field.0")     --??? # marc -> Marc
trim("field_with_spaces")                  <trim /> # "  marc  " -> marc
substring("title",0,1)                     <substring start="0" end="1"/> # marc -> m
prepend("title","die ")                    <compose  prefix="Hello,  " /># marc -> die marc
append("title"," must die")                <compose  postfix="!"/> # marc -> marc must die

# {author => "tom jones"}  -> {author => "senoj mot"}
reverse(author)   - # keine Entsprechung in morph
 
# {numbers => [1,14,2]} -> {numbers => [2,14,1]}
reverse(numbers)   - # keine Entsprechung in morph

# replace the value with a formatted (sprintf-like) version  - # keine Entsprechung in morph
# e.g. numbers: 
#         - 41
#         - 15
format(number,"%-10.10d %-5.5d") # numbers => "0000000041 00015"
# e.g. hash:
#        name: Albert
format(name,"%-10s: %s") # hash: "name      : Albert"

# date: "2015-03-07"  - # keine Entsprechung in morph /dateformat 
parse_text(date, '(\d\d\d\d)-(\d\d)-(\d\d)')
# date: 
#    - 2015
#    - 03
#    - 07

#  parses a text into an array or hash of values - # keine Entsprechung in morph
# date: "2015-03-07"
parse_text(date, '(\d\d\d\d)-(\d\d)-(\d\d)')
# date: 
#    - 2015
#    - 03
#    - 07 
 
# If you data record is:   <combine> /<concat>
#   a: eeny
#   b: meeny
#   c: miny
#   d: moe
paste(my.string,a,b,c,d)                 # my.string: eeny meeny miny moe
 
# Use a join character
paste(my.string,a,b,c,d,join_char:", ")  # my.string: eeny, meeny, miny, moe
 
# Paste literal strings with a tilde sign
paste(my.string,~Hi,a,~how are you?)     # my.string: Hi eeny how are you?

# date: "2015-03-07"
parse_text(date, '(?<year>\d\d\d\d)-(?<month>\d\d)-(?<day>\d\d)')
# date:
#   year: "2015"
#   month: "03" 
#   day: "07"
 
# date: "abcd"
parse_text(date, '(\d\d\d\d)-(\d\d)-(\d\d)')
# date: "abcd"

lookup("title","dict.csv", sep_char:'|')  # lookup 'marc' in dict.csv and replace the value
lookup("title","dict.csv", default:test)  # lookup 'marc' in dict.csv and replace the value or set it to 'test'
lookup("title","dict.csv", delete:1)    # lookup 'marc' in dict.csv and replace the value or delete nothing found

lookup_in_store('title', 'MongoDB', database_name:lookups)  # lookup the (id)-value of title in 'lookups' and
                                           # replace it with the data found
lookup_in_store('title', 'MongoDB', default:'default value' , delete:1) 

# Query a Solr index with the query stored in the 'query' field and overwrite it with all the results
search_in_store('query','Solr',url:"http://localhost:8983/solr",limit:10)

# Replace the data in foo.bar with an external file or url
import(foo.bar, JSON, file: "http://foo.com/bar.json", data_path: data.*)

add_to_store('authors.*', 'MongoDB', bag:authors, database_name:catalog)  # add matching values to a store as a side effect

add_to_exporter(data,CSV,header:1,file:/tmp/data.csv) # send the 'data' path to an alternative exporter
add_to_exporter(.,CSV,header:1,file:/tmp/data.csv)    # send the complete record to an alternative exporter

tb: Folgende Funktionen sind stark fokussiert auf Arrays. Hingegen operiert Metamorph bei ähnlichen Funktionen hier auch auf Feld/Objekt-Ebene und Arrays.

count("myarray")                           - # keine Entsprechung in morph in Morph zählt es die Felder nicht die Elemente eines Arrays # count number of elements in an array or hash
sum("numbers")                             - # keine Entsprechung in morph # replace an array element with the sum of its values
sort_field("tags")                         - # keine Entsprechung in morph # sort the values of an array
sort_field("tags", uniq:1)                 - # keine Entsprechung in morph # sort the values plus keep unique values
sort_field("tags", reverse:1)              - # keine Entsprechung in morph # revese sort
sort_field("tags", numeric:1)              - # keine Entsprechung in morph # sort numerical values
uniq(tags)                                 <unique> aber nicht nur array # strip duplicate values from an array
filter("tags","[Cc]at")                    - # regexp/equals but filter that only works on arrays # filter array values tags = ["Cats","Dogs"] => ["Cats"]
flatten(deep)                               - # keine Entsprechung in morph # {deep => [1, [2, 3], 4, [5, [6, 7]]]} => {deep => [1, 2, 3, 4, 5, 6, 7]}

cmd("java MyClass")                        # Use an external program that can read JSON 
                                           # from stdin and write JSON to stdout
perlcode("myscript.pl")                    # Execute Perl code as fix function
sleep(1,SECOND)                            # Do nothing for one second

split_field("foo",":")                     <split> aber ergebnis nicht automatisch array # marc:must:die -> ['marc','must','die']
join_field("foo",":")                      <concat> aber nicht nur ausgehend von array # ['marc','must','die'] -> marc:must:die
retain("id","id2","id3")                   # delete any field except 'id', 'id2', 'id3'
replace_all("title","a","x")               <replace> aber ohne regexp # marc -> mxrc

# Most functions can work also work on arrays. E.g.
replace_all("author.*","a","x")            <replace> aber ohne regexp # [ 'marc','jan'] => ['mxrc','jxn']
# Use:
#   authors.$end (last entry)
#   authors.$start (first entry)
#   authors.$append (last + 1)
#   authors.$prepend (first - 1)
#   authors.* (all authors)
#   authors.2 (3rd author)

collapse()                                 - # keine Entsprechung in morph# collapse deep nested hash to a flat hash
expand()                                   - # keine Entsprechung in morph# expand flat hash to deep nested hash
clone()                                    - # keine Entsprechung in morph # clone the perl hash and work on the clone
reject()                                   - # keine Entsprechung in morph # Reject (skip) a record
reject [condition]                         - # keine Entsprechung in morph # Reject a record on some condition:
                                           #   reject all_match(...)
                                           #   reject any_match(...)
                                           #   reject exists(...)
select()                                   - # keine Entsprechung in morph - # keine Entsprechung in morph# Select a record
select [condition]                         - # keine Entsprechung in morph # Select only those records that match a condition (see reject)

to_json('my.field')                        - # keine Entsprechung in morph # convert a value of a field to json
from_json('my.field')                      - # keine Entsprechung in morph # replace the json field with the parsed value

export_to_string('my.field',CSV,sep_char:";")   - # keine Entsprechung in morph # convert the value of a field into CSV
import_from_string('my.field',CSV,sep_char:";") - # keine Entsprechung in morph # replace a CSV field with the parsed value

error("eek!")                              - # keine Entsprechung in morph # abort the processing and say "eek!"
nothing()                                  - # keine Entsprechung in morph # do nothing (used in benchmarking)

# Include fixes from another file
include('/path/to/myfixes.txt')  - # include

# Send debug messages to a logger
log('test123')
log('hello world' , level: 'DEBUG')

# Boolean AND and OR, need a Condition + 'and'/'or' + a Fix 
exists(foo) and log('foo exists' , level: INFO) - auf Flux-Ebene
exists(foo) or log('foo doesnt exist' , level: INFO) - uaf Flux-Ebene
valid('', JSONSchema, schema: "my/schema.json") or log('this record is wrong', level: ERROR) - auf FluxEbene

# '3%A9' => 'café'
uri_decode(place)     - # keine Entsprechung in morph
# 'café' => '3%A9'
uri_encode(place)    <urlencode>

# Add a new field 'foo' with a random value between 0 and 9
random(foo, 10)  - # keine Entsprechung in morph

# Delete all the empty fields
vacuum()  - # keine Entsprechung in morph

# Copy all 245 subfields into the my.title hash
marc_map('245','my.title') 
# Copy the 245-$a$b$c subfields into the my.title hash in the order of the record
marc_map('245abc','my.title') 
# Copy the 245-$c$b$a subfields into the my.title hash in the order of the mapping
marc_map('245cba','my.title' , pluck:1) 
# Copy the 100 subfields into the my.authors array
marc_map('100','my.authors.$append') 
# Add the 710 subfields into the my.authors array
marc_map('710','my.authors.$append')
# Copy the 600-$x subfields into the my.subjects array while packing each into a genre.text hash
marc_map('600x','my.subjects.$append.genre.text')
# Copy the 008 characters 35-35 into the my.language hash
marc_map('008_/35-35','my.language')
# Copy all the 600 fields into a my.stringy hash joining them by '; '
marc_map('600','my.stringy', join:'; ')
# When 024 field exists create the my.has024 hash with value 'found'
marc_map('024','my.has024', value:found)
# Do the same examples now with the marc fields in 'record2'
marc_map('245','my.title', record:record2)
# Remove the 900 fields
marc_remove('900')
# Add a marc field (in Catmandu::MARC 0.110)
marc_add('999', ind1, ' ' , ind2, '1' , a, 'test123')
# Add a marc field populated with data from your record
marc_add('245', a , $.my.title.field, c , $.my.author.field)
# Set a marc value of one (sub)field to a new value
marc_set('LDR/6','p')
marc_set('650p','test')
marc_set('100[3]a','Farquhar family.')

# Map all 650 subjects into an array 
marc_map('650','subject', join:'###') 
split_field('subject','###')

# uppercase the value of field 'foo' if all members of 'oogly' have the value 'doogly'
if all_match('oogly.*', 'doogly')
  upcase('foo') # foo => 'BAR'
else
  downcase('foo') # foo => 'bar'
end

# inverted
unless all_match('oogly.*', 'doogly')
  upcase('foo') # foo => 'BAR'
end;

# uppercase the value of field 'foo' if field 'oogly' has the value 'doogly'
if any_match('oogly', 'doogly')
  upcase('foo') # foo => 'BAR'
end

# inverted
unless any_match('oogly', 'doogly')
  upcase('foo') # foo => 'BAR'
end

# uppercase the value of field 'foo' if the field 'oogly' exists
if exists('oogly')
  upcase('foo') # foo => 'BAR'
end

# inverted
unless exists('oogly')
  upcase('foo') # foo => 'bar'
end

# add a new field when the 'year' field is equal to 2018
if all_equal('year','2018')
 add_field('my.funny.title','true')
end

# add a new field when at least one of the 'year'-s is equal to 2018
if any_equal('years.*','2018')
 add_field('my.funny.title','true')
end

# compare things (needs Catmandu 0.92 or better)
if greater_than('year',2000)
  add_field('recent','yes')
end

if less_than('year',1970)
  add_field('ancient','yes')
end

# execute fixes if one path is contained in another
# foo => 1 , bar => [3,2,1]  => in(foo,bar) -> true
if in(foo,bar)
   add_field(test,ok)
end

# only execute fixes if all path values are the boolean true, 1 or "true"
if is_true(data.*.has_error)
  add_field(error,yes)
end

# only execute fixes if all path values are the boolean true, 0 or "false"
if is_false(data.*.has_error)
  add_field(error,no)
end

# only execute the fixes if the path contains an array
if is_array(data)
  upcase(data.0)
end

# only execute the fixes if the path contains an object (an hash, nested field)
if is_object(data)
  add_field(data.ok,yes)
end

# only execute the fixes if the path contains a number
if is_number(data)
  append(data," : is a number")
end

# only execute the fixes if the path contains a string
if is_string(data)
  append(data," : is a string")
end

# only execute the fixes if the path contains 'null' values
if is_null(data)
  set_field(data,"I'm empty!")
end

# Evaluates true when one or all marc (sub)fields match a regular expression
if marc_all_match('245','My funny title')
  add_field('funny.title','yes')
end
if marc_all_match('LDR/6','c')
  marc_set('LDR/6','p')
end

# Evaluates to true when at least one of the marc (sub)fields match a regular expression
if marc_any_match('650','catmandu')
  add_field('important.books','yes')
end


# Evaluates true when the JSON fragment is valid against a JSON Schema
if valid(data,JSONSchema,schema:myschema.json)
   ...
end

## Binds (needs Catmandu 0.92 or better)

# The identity binder doesn't embody any computational strategy. It simply 
# applies the bound fix functions sequentially to its input without any 
# modification.
do identity()
  add_field(foo,bar)
  add_field(foo2,bar2)
end

# Maybe, computes all the fix functions and ignores fixes once they throw errors
# or return undef.
do maybe()
  foo()
  return_undef() # rest will be ignored
  bar()
end

# List over all items in demo and add a foo => bar field
# { demo => [{},{},{}] } => { demo => [{foo=>bar},{foo=>bar},{foo=>bar}]}
do list(path: demo)
  add_field(foo,bar)
end

# Print statistical information on the processing speed of fixes to the standaard error.
do benchmark(output:/dev/stderr)
  foo()
end

# Find all ISBN in a stream
do hashmap(exporter: JSON, join:',')
  # Need an identity binder to group all operations that calculate key_value pairs
  do identity()
   copy_field(isbn,key)
   copy_field(_id,value)
  end
end

# Count the number of ISBN occurrences in a stream
do hashmap(count: 1)
  copy_field(isbn,key)
end

# Filter out an array (needs Catmandu 0.9302 or better)
#    data:
#       - name: patrick
#       - name: nicolas
# to:
#    data:
#       - name: patrick
do with(path:data)
  reject all_match(name,nicolas)
  # Or:
  # if all_match(name,nicolas)
  #  reject()
  # end
end

#  run fixes that should run within a time limit
do timeout(time => 5, units => seconds)
  ...
end

# a binder that computes Fix-es for every element in record
do visitor()
   # upcase all the 'name' fields in the record
   if all_match(key,name)
     upcase(scalar)
   end
end

# a binder runs fixes on records from an importer
do importer(OAI,url: "http://lib.ugent.be/oai") 
  retain(_id)
  add_to_exporter(.,YAML)
end
```