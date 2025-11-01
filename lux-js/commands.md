# Develop
```
cd ~/lux/lux-js/ \
&& lux with js clean \
&& lux with js auto build

cd ~/lux/lux-js/ \
&& lux with stable clean \
&& lux with stable auto build

cd ~/lux/lux-js/ \
&& lux with jvm clean \
&& lux with jvm auto build
```

# Build

```
## Build JVM-based compiler
cd ~/lux/lux-js/ \
&& lux with jvm clean \
&& lux with jvm build \
&& mv target/program.jar jvm_based_compiler.jar

## Use JVM-based compiler to produce a JS/Node-based compiler.
cd ~/lux/lux-js/ \
&& lux clean \
&& time java -Xss2m -jar jvm_based_compiler.jar build --source ~/lux/lux-js/source --source ~/lux/stdlib/source --target ~/lux/lux-js/target --module program --program _ \
&& mv target/program.js node_based_compiler.js

## Use JS/Node-based compiler to produce another JS/Node-based compiler.
cd ~/lux/lux-js/ \
&& lux clean \
&& node --stack_size=8192 node_based_compiler.js build --source ~/lux/lux-js/source --source ~/lux/stdlib/source --target ~/lux/lux-js/target --module program --program _
```

# Try

```
## Compile Lux's Standard Library's tests using a JS/Node-based compiler.
cd ~/lux/stdlib/ \
&& lux clean \
&& node --stack_size=8192 ~/lux/lux-js/target/program.js build --source ~/lux/stdlib/source --target ~/lux/stdlib/target --module test/lux --program _ \
&& node ~/lux/stdlib/target/program.js
```

# Deploy

```
cd ~/lux/lux-js/ \
&& mvn install:install-file -Dfile=target/program.js -DgroupId=com.github.luxlang -DartifactId=lux-js -Dversion=0.9.0-SNAPSHOT -Dpackaging=js

cd ~/lux/lux-js/ && mvn deploy:deploy-file \
-Durl=https://$NEXUS_USERNAME:$NEXUS_PASSWORD@oss.sonatype.org/content/repositories/snapshots/ \
-Dfile=target/program.js \
-DgroupId=com.github.luxlang \
-DartifactId=lux-js \
-Dversion=0.9.0-SNAPSHOT \
-Dpackaging=js
```

# Release

```
LUX_PROJECT=lux-js && \
LUX_VERSION=0.9.0 && \
cd ~/lux/$LUX_PROJECT/ && \
mkdir RELEASE && \
mkdir RELEASE/com && \
mkdir RELEASE/com/github && \
mkdir RELEASE/com/github/luxlang && \
mkdir RELEASE/com/github/luxlang/$LUX_PROJECT && \
mkdir RELEASE/com/github/luxlang/$LUX_PROJECT/$LUX_VERSION && \
lux pom && \
mv pom.xml RELEASE/com/github/luxlang/$LUX_PROJECT/$LUX_VERSION/$LUX_PROJECT-$LUX_VERSION.pom && \
cp target/program.js RELEASE/com/github/luxlang/$LUX_PROJECT/$LUX_VERSION/$LUX_PROJECT-$LUX_VERSION.js && \
cd RELEASE/com/github/luxlang/$LUX_PROJECT/$LUX_VERSION && \
touch README.md && \
zip $LUX_PROJECT-$LUX_VERSION-sources.jar README.md && \
zip $LUX_PROJECT-$LUX_VERSION-javadoc.jar README.md && \
rm README.md && \
for file in *.js *.jar *.pom; do md5sum $file | awk '{ print $1 }' > $file.md5; done && \
for file in *.js *.jar *.pom; do sha1sum $file | awk '{ print $1 }' > $file.sha1; done && \
for file in *.*; do gpg -ab $file; done && \
cd ../../../../.. && zip release.zip com/github/luxlang/$LUX_PROJECT/$LUX_VERSION/*
```

