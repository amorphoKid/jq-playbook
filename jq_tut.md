## get results from star wars api
### decode array
curl -s https://swapi.dev/api/people/ | jq '.results'
### filter by attribute
curl -s "https://swapi.dev/api/people/"|jq '.results'|jq '.[] |\
select(.eye_color=="blue")' 
### restructure output
curl -s "https://swapi.dev/api/people/"|jq '.results'|jq '.[]|{name:.name, eyes:.eye_color}'
## openlibrary
### use less to reverse output
curl -s "https://openlibrary.org/search.json?q=st%C4%99pniak"|less
### export http response to file
curl -s "https://openlibrary.org/search.json?q=st%C4%99pniak" > openlibrary.json
### get author and pub year by taking first entries of lists
jq '.docs[]|{title,author_name:.author_name[0],publish_year:.publish_year[0]}' openlibrary.json
### prefilter non nulls
jq '.docs[]|select(.publish_year!=null and .author_name!=null)|{title,author_name:.author_name[],publish_year:.publish_year[]}' openlibrary.json
### rebuils result list by '[]'
jq '[.docs[]|select(.publish_year!=null and .author_name!=null)|{title,author_name:.author_name[],publish_year:.publish_year[]}]' openlibrary.json
### sort_by
jq '[.docs[]|select(.publish_year!=null and .author_name!=null)|{title,author_name:.author_name[],publish_year:.publish_year[]}]|sort_by(.publish_year)' openlibrary.json
### reverse output
jq '[.docs[]|select(.publish_year!=null and .author_name!=null)|{title,author_name:.author_name[],publish_year:.publish_year[]}]|sort_by(.publish_year)|reverse' openlibrary.json
### count entries in response
jq '[.docs[]|select(.publish_year!=null and .author_name!=null)|{title,author_name:.author_name[],publish_year:.publish_year[]}]|sort_by(.publish_year)|reverse|length' openlibrary.json
### limit # entries in response
jq '[.docs[]|select(.publish_year!=null and .author_name!=null)|{title,author_name:.author_name[],publish_year:.publish_year[]}]|sort_by(.publish_year)|reverse|limit(3;.[])' openlibrary.json
### reconstruct array
jq '[.docs[]|select(.publish_year!=null and .author_name!=null)|{title,author_name:.author_name[],publish_year:.publish_year[]}]|sort_by(.publish_year)|reverse|[limit(3;.[])]' openlibrary.json
### group by author and count group entries
jq '[.docs[]|select(.publish_year!=null and .author_name!=null)|{title,author_name:.author_name[],publish_year:.publish_year[]}]|group_by(.author_name)|length' openlibrary.json
### group by author and count publications
#### set up countable obejct by identy '.' operator
jq '[.docs[]|select(.publish_year!=null and .author_name!=null)|{title,author_name:.author_name[],publish_year:.publish_year[]}]|group_by(.author_name)|.[]|{name:.[0].author_name, count:.}' openlibrary.json
#### pipe countable obj to length function
jq '[.docs[]|select(.publish_year!=null and .author_name!=null)|{title,author_name:.author_name[],publish_year:.publish_year[]}]|group_by(.author_name)|.[]|{name:.[0].author_name, count:.|length}' openlibrary.json
### construct result list and sort by count
jq '[.docs[]|select(.publish_year!=null and .author_name!=null)|{title,author_name:.author_name[],publish_year:.publish_year[]}]|[group_by(.author_name)|.[]|{name:.[0].author_name, count:.|length}]|sort_by(.count)' openlibrary.json
### top 3 authors
jq '[.docs[]|select(.publish_year!=null and .author_name!=null)|{title,author_name:.author_name[],publish_year:.publish_year[]}]|[group_by(.author_name)|.[]|{name:.[0].author_name, count:.|length}]|sort_by(.count)|reverse|[limit(3;.[])]' openlibrary.json
### generate list of authors
jq '[.docs[].author_name[0]]|length' openlibrary.json
## export csv table
jq -r '["Titel","Autor","Jahr"],(.docs[]|[.title,.author_name[0],.publish_year[0]])|@csv' openlibrary.json > book_list.csv 
