# svn2git
Migrating repositories from Subversion (SVN) to Git with history

## SVN to GIT migration
```
java -jar svn-migration-scripts.jar authors $SVN_URL > authors_map
sed -i 's/mycompany/stateauto/g' authors_map
```

## Create GIT repo with authors file and without SVN revision in commit message
```
git svn clone SVN_URL --authors-file=authors_map --no-metadata --prefix "" -s -r<start>:<end> <new_DIRECTORY> --stdlayout
Note: <start>,<end> are the SVN revision numbers 
```

## Fix tags
```
cd <new_DIRECTORY>
for t in $(git for-each-ref --format='%(refname:short)' refs/remotes/tags); do git tag ${t/tags\//} $t && git branch -D -r $t; done
```

## Setup branches/tags to be ready to push to remote git server
```
for b in $(git for-each-ref --format='%(refname:short)' refs/remotes); do git branch $b refs/remotes/$b && git branch -D -r $b; done
```

## Delete trunk 
```
git branch -d trunk
```

## Push everything to git remote
```
git remote add origin GIT_URL
```

## Master branch 
```
git fetch origin
git merge -s recursive -X theirs origin/master --allow-unrelated-histories -m "Migrating SVN history"
```

## All branches
```
git checkout <SVN_BranchName>
git fetch origin
git merge -s recursive -X theirs origin/<SVN_BranchName> --allow-unrelated-histories -m "Migrating SVN history"
```
## Lastly
```
git push origin --all
git push origin --tags
```

##### **This is built with the help of [Atlassian docs](https://www.atlassian.com/git/tutorials/migrating-overview).
