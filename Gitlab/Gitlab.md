## GITLAB安装

#### 下载gitlab安装包
`wget --content-disposition https://packages.gitlab.com/gitlab/gitlab-ce/packages/ubuntu/focal/gitlab-ce_16.5.8-ce.0_amd64.deb/download.deb`

#### 安装gitlab
`sudo dpkg -i gitlab-ce_16.5.8-ce.0_amd64.deb`

#### 配置gitlab
`sudo vim /etc/gitlab/gitlab.rb` 
* 修改external_url为本机ip，允许加端口号
* 修改时区，UTC改成Asia/Shanghai

#### gitlab内存优化
```
# 减少数据库缓存
`postgresql['shared_buffers'] = "64MB"`
# 减少数据库并发数
`postgresql['max_worker_processes'] = 4`
# 禁用puma cluster模式，可以减少资源占用
`puma['worker_processes'] = 0`
# 减少sidekiq并发数
sidekiq['concurrency'] = 2
sidekiq['max_concurrency'] = 2`
# 禁用监控
prometheus_monitoring['enable'] = false
# 减少gitlab_rails组件的内存消耗
gitlab_rails['env'] = {
   'MALLOC_CONF' => 'dirty_decay_ms:500,muzzy_decay_ms:500'
}
```

#### gitlab操作命令
```
# 停止gitlab服务
`sudo gitlab-ctl stop`
# 启动gitlab服务
`sudo gitlab-ctl reconfigure`
# 启动所有gitlab组件
`sudo gitlab-ctl start`
# 启用开机自启动
`sudo systemctl enable gitlab-runsvdir.service`
# 查看root密码（24小时有效）
`cat /etc/gitlab/initial_root_password`
```

## gitlab-runner安装
#### 下载安装包
`sudo apt-get install gitlab-runner`

#### 配置Gitlab Runner
* 通过 群组->设置->CI/CD->Runner 查找对应的URL和注册令牌
* `sudo gitlab-runner register`填写的对应的参数即可

## CI/CD流程引入
```
variables:
  REMOTE_URL:
  HOTFIX_BRANCH: hotfix
  TEST_MERGE_BRANCH: test-merge
  ACCESS_TOKEN:

stages:
  - merge

branch_auto_merge:
  stage: merge
  script:
    - git remote set-url origin $REMOTE_URL;
    - |
      if [[ $CI_COMMIT_REF_NAME =~ ^(develop|feature|patch).* ]]; then
        TARGET_BRANCH=$TEST_MERGE_BRANCH;
      elif [[ $CI_COMMIT_REF_NAME =~ ^release.* ]]; then
        TARGET_BRANCH=$HOTFIX_BRANCH
      else
        echo "Error: Branch name does not match any known patterns."
        exit 1
      fi;
    - git checkout $TARGET_BRANCH && git pull origin $TARGET_BRANCH && git reset --hard $TARGET_BRANCH;
    - |
      if [ -z "$(git diff --name-only origin/$CI_COMMIT_REF_NAME)" ]; then
        echo "No changes detected. Skipping merge.";
        exit 0
      fi;
    - git merge --no-commit --no-ff origin/$CI_COMMIT_REF_NAME;
    - git commit -m "Merge branch '${CI_COMMIT_REF_NAME}' into '${TARGET_BRANCH}'";
    - git push origin $TARGET_BRANCH;
  #  after_script:
  #    - 'curl -X POST "https://oapi.dingtalk.com/robot/send?access_token='${ACCESS_TOKEN}'" -H "Content-Type: application/json" -d "{\"msgtype\": \"text\", \"text\": {\"content\": \"'${TEST_MERGE_BRANCH}' conflicts occurred! Files:\n $(git diff --name-only --diff-filter=U)\"}}"'
  only:
    - /^(develop|feature|patch|release).*/
```


