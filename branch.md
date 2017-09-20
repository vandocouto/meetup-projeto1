
Master - Staging
Passo 1
<pre>
git branch
* master
  staging
</pre>
git checkout staging

<pre>
git branch
  master
* staging
</pre>

<pre>
$ git merge master
Updating 87c0f67..8e3aae1
Fast-forward
 ansible/templates/production.docker-compose.yml |  2 +-
 ansible/templates/staging.docker-compose.yml    |  2 +-
 branch                                          | 33 +++++++++++++++++++++++++++++----
 merge.txt                                       | 34 +++++++++++++++++++++++++++++++++-
 4 files changed, 64 insertions(+), 7 deletions(-)
</pre>

<pre>
 git branch
  master
* staging
</pre>

<pre>
git push origin stating
</pre>
<pre>
git checkout master
M	branch
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.
</pre>

<pre>
git branch
* master
  staging
</pre>

Staging - Master