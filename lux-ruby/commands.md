# Build

```
## Develop
cd ~/lux/lux-ruby/ \
&& lux clean \
&& lux auto build
```

# Try

```
## Compile Lux's Standard Library's tests using a JVM-based compiler.
cd ~/lux/stdlib/ \
&& lux clean \
&& java -jar ~/lux/lux-ruby/target/program.jar build --source ~/lux/stdlib/source --target ~/lux/stdlib/target --module test/lux --program _ \
&& RUBY_THREAD_VM_STACK_SIZE=15700000 ruby ~/lux/stdlib/target/program/main.rb
```

# Deploy

```
cd ~/lux/lux-ruby/ \
&& mvn install:install-file -Dfile=target/program.jar -DgroupId=com.github.luxlang -DartifactId=lux-ruby -Dversion=0.9.0-SNAPSHOT -Dpackaging=jar

cd ~/lux/lux-ruby/ && mvn deploy:deploy-file \
-Durl=https://$NEXUS_USERNAME:$NEXUS_PASSWORD@oss.sonatype.org/content/repositories/snapshots/ \
-Dfile=target/program.jar \
-DgroupId=com.github.luxlang \
-DartifactId=lux-ruby \
-Dversion=0.9.0-SNAPSHOT \
-Dpackaging=jar
```

# Release

```
LUX_PROJECT=lux-ruby && \
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
cp target/program.jar RELEASE/com/github/luxlang/$LUX_PROJECT/$LUX_VERSION/$LUX_PROJECT-$LUX_VERSION.jar && \
cd RELEASE/com/github/luxlang/$LUX_PROJECT/$LUX_VERSION && \
touch README.md && \
zip $LUX_PROJECT-$LUX_VERSION-sources.jar README.md && \
zip $LUX_PROJECT-$LUX_VERSION-javadoc.jar README.md && \
rm README.md && \
for file in *.jar *.pom; do md5sum $file | awk '{ print $1 }' > $file.md5; done && \
for file in *.jar *.pom; do sha1sum $file | awk '{ print $1 }' > $file.sha1; done && \
for file in *.*; do gpg -ab $file; done && \
cd ../../../../.. && zip release.zip com/github/luxlang/$LUX_PROJECT/$LUX_VERSION/*
```

