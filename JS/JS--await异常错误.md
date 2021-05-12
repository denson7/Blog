### async/await 中的异常处理
```javascript
// 方法1：使用try...catch
try {
  await getUser();
} catch(e) {
  console.log(e);
}
// 方法2：
let user = await getUser().catch(e => console.log('Error: ', e.message));
let friendsOfUser = await getFriendsOfUser(userId).catch(e => console.log('Error: ', e.message));
let posts = await getUsersPosts(userId).catch(e => console.log('Error: ', e.message));

// 方法3
/**
 * @description  Returns Go responses(data, err)
 * @param {Promise} promise
 * @returns {Promise} [ data, undefined ]
 * @returns {Promise} [ undefined, Error ]
 */
const handle = (promise) => {
  return promise
    .then(data => ([data, undefined])) // 类似golang，第一个参数返回err,第二个参数返回值 [null, data]
    .catch(error => Promise.resolve([undefined, error]));
};

async function userProfile() {
  let [user, userErr] = await handle(getUser());
  if (userErr) throw new Error('Could not fetch user details');

  let [friendsOfUser, friendErr] = await handle(getFriendsOfUser(userId));
  if (friendErr) throw new Error('Could not fetch user\'s friends');

  let [posts, postErr] = await handle(getUsersPosts(userId));
  if (postErr) throw new Error('Could not fetch user\'s posts');
}
```
