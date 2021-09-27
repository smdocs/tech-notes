# Bash Notes

Following are two ways you can change strings in multiple files simultaneously.

1. Change strings in files in the same directory. If I only wanted to change the strings in the files in the present directory, I could use:
```bash
  sed -i 's/user_/admin_/g' *.yml
```

Here, I used the sed command with the -i argument to make the changes in place. The /g at the end of the parameter means replace all occurrences in each file.

2. Change strings in files in the current and subdirectories

In my example scenario, there are files in subdirectories that also need to be changed. For that, I use the find command again:

```bash
  find ./ -type f | xargs sed -i 's/user_/admin_/g'
```

### How to check the Network interface details;

1. <b>ethtool command</b>
  -- The ethtool command is used to query or control network driver and hardware settings.
  ```# sudo ethtool eth0 ```
  
 2. Check all available interfaces
 ```ip a |awk '/state UP/{print $2}'```
