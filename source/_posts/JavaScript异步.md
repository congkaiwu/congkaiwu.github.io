---
title: JavaScript异步
date: 2019-05-29 23:40:31
tags:
---

## Promise
``` bash
getUser(1)
    .then(user => getRepositories(user.gitHubUsername))
    .then(repos => getCommits(repos[1]))
    .then(commits => console.log('Complete.'))
    .catch(err => console.log('err' + err.message));

function getUser(id) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('getUser');
            // reject(new Error('message'));
            resolve({id: id, gitHubUsername: 'user1'});
        }, 1000);
    });
}

function getRepositories(username) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('getRepositories');
            resolve(['repo1', 'repo2', 'repo3']);
        }, 1000);
    });
}

function getCommits(repo) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            console.log('getCommits');
            resolve(['commit1', 'commit2', 'commit3']);
        }, 1000);
    });
}
```

##  await 和 async
``` bash
async function displayCommits() {
    try{
        const user = await getUser(1);
        const repos = await getRepositories(user.gitHubUsername);
        const commits = await getCommits(repos[1]);
        console.log('Complete.')
    } catch (err) {
        console.log('err' + err.message);
    }
}

displayCommits();
```