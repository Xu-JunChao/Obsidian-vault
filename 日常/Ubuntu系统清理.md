```
pip cache purge && rm -rf ~/.cache/uv ~/.cache/go-build ~/.cache/yarn ~/.cache/JetBrains


# npm 缓存（安全）
npm cache clean --force

# bun 缓存（安全）
rm -rf ~/.bun

# KDE baloo 索引缓存（安全，会自动重建）
rm -rf ~/.local/share/baloo

# 钉钉缓存（4G，清理后需重新登录）
rm -rf ~/.config/DingTalk


docker system prune -a --volumes

conda clean --all
go clean -modcache
```
 
```


```