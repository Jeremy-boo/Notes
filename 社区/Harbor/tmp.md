##

### update_db

```
_update_db:
	@echo "update goharbor db Dockerfile.base"
	@$(SEDCMDI) 's/RUN tdnf install -y shadow gzip postgresql/RUN tdnf install -y shadow gzip postgresql13/g' $(HARBOR_PHOTON_DB_PATH)
	@$(SEDCMDI) 's/usr\/share\/postgresql\/postgresql.conf.sample/usr\/pgsql\/13\/share\/postgresql.conf.sample/g' $(HARBOR_PHOTON_DB_PATH)
	@$(SEDCMDI) '14 a \\t&& ln -s /usr/pgsql/13/bin/* /usr/bin/ \\' $(HARBOR_PHOTON_DB_PATH)
```



## Harbor机器人账户

```
HFcMj50saHEFWgUMOvzZQZTcQnumUt7Q
```

