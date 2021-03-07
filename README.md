
# arxiv-fzf

`arxiv-fzf` is a simple command line interface for searching papers on
[arXiv](https://arxiv.org/).

This solution was inspired by the original, single line version of
[ytfzf](https://github.com/pystardust/ytfzf).

### Usage:

`axiv-fzf <search-terms>`

The output is a url to the paper's abstract page. You can then pipe this
into your favorite browser, e.g.

`... | xargs firefox`, or `... | xargs open`, in mac os.


### Dependencies:

- [fzf](https://github.com/junegunn/fzf) -- for fuzzy finding.
- [yq](https://github.com/kislyuk/yq) (which depends on
[jq](https://github.com/stedolan/jq)) -- for parsing arxiv's api response.

### Full code

```bash
#!/bin/bash
if [[ ! -z $1 ]]; then
  echo $* | tr ' ' '+' | xargs -I'{}' curl -s \
  "http://export.arxiv.org/api/query?search_query=all:{}&max_results=20" | \
  xq ".feed.entry[] | .title[:66], (.author | if type==\"array\" then \
  [.[].name] else [.name] end | join(\", \"))[:48], .published[:4], .id" | \
  sed 's/\\n//g;N;s/\n/\t/;N;s/\n/\t/;N;s/\n/\t/;s|http:.*/||' | \
  awk -F \t '{printf "%-68s | %-50s | %-6s | %-13s \n", $1, $2, $3, $4}' | \
  sed 's/"//g' | fzf | sed "s/.*| *//;s|^|http://arxiv.org/abs/|"
fi
```

