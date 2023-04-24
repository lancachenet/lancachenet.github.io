source "https://rubygems.org"

# Hello! This is where you manage which Jekyll version is used to run.
# When you want to use a different version, change it below, save the
# file and run `bundle install`. Run Jekyll with `bundle exec`, like so:
#
#     bundle exec jekyll serve
#
# This will help ensure the proper Jekyll version is running.
# Happy Jekylling!
#gem "jekyll", "~> 3.7.3"


# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
gem "github-pages", group: :jekyll_plugins


platforms :mingw, :x64_mingw, :mswin, :jruby do
    # Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem and associated library.
    gem "tzinfo", ">= 1", "< 3"
    gem "tzinfo-data"

    # Ruby 3.0 does not ship with webrick anymore. Seems to only be needed on the Windows version of Ruby
    gem "webrick", "~> 1.7"
  end