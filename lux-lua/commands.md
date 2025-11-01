# Build

```
## Develop
cd ~/lux/lux-lua/ \
&& lux clean \
&& lux auto build
```

# Try

```
## Compile Lux's Standard Library's tests using a JVM-based compiler.
cd ~/lux/stdlib/ \
&& lux clean \
&& java -jar ~/lux/lux-lua/target/program.jar build --source ~/lux/stdlib/source --target ~/lux/stdlib/target --module test/lux --program _ \
&& ~/lua-5.4.2/install/bin/lua ~/lux/stdlib/target/program.lua
```

# Deploy

```
cd ~/lux/lux-lua/ \
&& mvn install:install-file -Dfile=target/program.jar -DgroupId=com.github.luxlang -DartifactId=lux-lua -Dversion=0.9.0-SNAPSHOT -Dpackaging=jar

cd ~/lux/lux-lua/ && mvn deploy:deploy-file \
-Durl=https://$NEXUS_USERNAME:$NEXUS_PASSWORD@oss.sonatype.org/content/repositories/snapshots/ \
-Dfile=target/program.jar \
-DgroupId=com.github.luxlang \
-DartifactId=lux-lua \
-Dversion=0.9.0-SNAPSHOT \
-Dpackaging=jar
```

# Release

```
LUX_PROJECT=lux-lua && \
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

