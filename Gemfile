source 'https://rubygems.org'

require 'json'
require 'open-uri'
versions = JSON.parse(open('https://pages.github.com/versions.json').read)

# GitHub Pages required gems
gem 'jekyll', versions['jekyll']
gem 'jekyll-coffeescript', versions['jekyll-coffeescript']
gem 'jekyll-sass-converter', versions['jekyll-sass-converter']
gem 'kramdown', versions['kramdown']
gem 'maruku', versions['maruku']
gem 'rdiscount', versions['rdiscount']
gem 'redcarpet', versions['redcarpet']
gem 'RedCloth', versions['RedCloth']
gem 'liquid', versions['liquid']
gem 'pygments.rb', versions['pygments.rb']
gem 'jemoji', versions['jemoji']
gem 'jekyll-mentions', versions['jekyll-mentions']
gem 'jekyll-redirect-from', versions['jekyll-redirect-from']
gem 'jekyll-sitemap', versions['jekyll-sitemap']
gem 'github-pages', versions['github-pages']

# Gist support
gem 'jekyll-gist'
