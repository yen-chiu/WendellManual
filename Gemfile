# 官方源。GitHub Actions 上访问 gems.ruby-china.com 会因证书校验失败导致
# bundle 直接退出（exit 17），所以这里用 rubygems.org；
# 本机在国内如需加速，可临时改回镜像，但**不要提交**。
source "https://rubygems.org"

gemspec

gem "jekyll-github-metadata", ">= 2.15"

gem "jekyll-include-cache", group: :jekyll_plugins
gem "jekyll-sitemap", group: :jekyll_plugins

gem "html-proofer", "~> 5.0", :group => :development

# debug
gem "csv"
gem "base64"
gem "faraday-retry"
gem "wdm", ">= 0.1.0", platforms: [:mswin, :mingw, :x64_mingw]
gem "zeitwerk", "~> 2.7.1"
gem "sass-embedded", "< 1.82.0"
