task :default do
  sh('sass --update sass/styles.scss:styles.css') { |ok,_| fail 'problem running sass'      unless ok }
  sh('bookingit build ../angular-rails.com/')     { |ok,_| fail 'problem running bookingit' unless ok }
end
