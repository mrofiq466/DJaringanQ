## 1. Install add-ons
```
apt update && apt install gcc shc -y
```

## 2. How to use
```
cat << 'EOF' > hello.sh
echo hello $1
EOF

shc -f hello.sh
```

## 3. Verify
```
cat hello.sh
cat hello.sh.x
bash hello.sh.x test
```

## Noted
Use the command below, so it can be used on another host.
```
shc -r -f hello.sh -o hello
```

## Reference
- https://www.simplified.guide/bash/compile-script
- https://github.com/neurobin/shc/issues/98
- https://github.com/neurobin/shc/issues/15


[Back](../)
