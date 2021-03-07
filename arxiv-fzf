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
