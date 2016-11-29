# Set up calabash with Travis via Fastlane

I've fixed the problem with Travis and Calabash and I did it with fastlane, so this is the result:

My app calabash app was created with the third method, create a new target: https://github.com/calabash/calabash-ios/wiki/Tutorial%3A--Creating-a-cal-Target   

Unfortunately we can't use gym but we can still running xcodebuild and xcpretty

This is the lane in fastlane: 

```
desc "Build calabash app"
lane :build_cal do
     match(type: "development", app_identifier: "bundle id calabash app", git_url: "git code signing repo", username: "username") # more information: https://codesigning.guide
     sh("xcrun xcodebuild -workspace ../workSpace.xcworkspace -scheme scheme -configuration Debug -destination 'platform=iOS Simulator,name=iPhone 6s' -sdk iphonesimulator ARCHS=\"i386 x86_64\" VALID_ARCHS=\"i386 x86_64\" ONLY_ACTIVE_ARCH=NO -derivedDataPath ../build build | xcpretty")
 
 end
```

And then in the travis file we only need to run the line:

```
script:
 - bundle exec fastlane build_cal
 - bundle exec cucumber
```

Cucumber will find automatically the project, but if you have problems you can help him with:

```
- XCODEPROJ="myProject.xcodeproj" bundle exec cucumber
```

The only thing we have to take into consideration with travis and match is the password:

## Using Match with travis

With XCode 8 we are using match in travis. In order to make the process work we need to set the environment variables for:

`FASTLANE_PASSWORD`= this is the password for the Apple account
`MATCH_PASSWORD`= this is the passphrase for the certificate repo

The way to add them in travis.yml is:

```
travis encrypt "MATCH_PASSWORD=\\!foo\\!" --add
```
