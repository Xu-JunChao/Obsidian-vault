```
bash /home/xjc/Code/python/case_repo/hnkj/hn-python/algorithm/scripts/long_term_enterprise_load_train.sh, --data-name-list  algorithm/dataset/forecasting/long_term_enterprise_load_train/10100000/input_data.csv --save-path output/long_term_enterprise_load_train/10100000 --save-checkpoint-name algorithm/checkpoints/long_term_enterprise_load_train/10100000/checkpoint.pth --ind-b-no-count 2      




bash /home/xjc/Code/python/case_repo/hnkj/hn-python/algorithm/scripts/long_term_enterprise_load_train.sh  --data-name-list long_term_enterprise_load_train/10100000/input_data.csv --save-path long_term_enterprise_load_train/10100000 --save-checkpoint-name long_term_enterprise_load_train/10100000/checkpoint.pth --ind-b-no-count 2



bash  home/xjc/Code/python/case_repo/hnkj/hn-python/algorithm/scripts/long_term_enterprise_load_pred.sh --data-name-list long_term_enterprise_load/1934527940951810050/10100000/input_data.csv --save-path long_term_enterprise_load/1934527940951810050/10100000 --load-checkpoint-path ./algorithm/checkpoints/long_term_enterprise_load_train/10100000/checkpoint.pth --ind-b-no-count 2
```