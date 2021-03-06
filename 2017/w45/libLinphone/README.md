
# liblinphone &  bellesip  &  ms2  & ORTP 之间的关系

liblinphone是上层应用,使用bellesip进行sip信令的交互，使用ms2进行媒体流的交互。
ms2用于设置各种编解码(G.729 264 VP8)并使用oRTP进行传输。





# linphone分析

以linphonec.c作为出发点


```
int main()
{

if (! linphonec_init(argc, argv) ) exit(EXIT_FAILURE);

}
```



```
int
linphonec_init(int argc, char **argv)
{

/*
* Initialize linphone core
*/

linphonec=linphone_core_new (&linphonec_vtable, configfile_name, factory_configfile_name, NULL);
}

```

跳转到：linphonecore.c


```
static LinphoneCore *_linphone_core_new(const LinphoneCoreVTable *vtable,
						const char *config_path, const char *factory_config_path, void * userdata) {
	LinphoneCore *lc;
	LpConfig *config = lp_config_new_with_factory(config_path, factory_config_path);
	lc=linphone_core_new_with_config(vtable, config, userdata);
	lp_config_unref(config);
	return lc;
}
```


```
LinphoneCore *_linphone_core_new_with_config(LinphoneCoreCbs *cbs, struct _LpConfig *config, void *userdata) {
	LinphoneCore *core = belle_sip_object_new(LinphoneCore);
	linphone_core_init(core, cbs, config, userdata);
	return core;
}
```



**真正的初始化函数：
下边是真正的初始化函数登场：**




```
static void linphone_core_init(LinphoneCore * lc, LinphoneCoreCbs *cbs, LpConfig *config, void * userdata){


	ms_message("================================================   \n");
	ms_message("=============linphone_core_init=================   \n");
	ms_message("================================================   \n");
	
	
	
    ms_message("Initializing LinphoneCore %s", linphone_core_get_version());
	
	
    ms_message("================================================   \n");
	ms_message("=============ortp_init()初始化=================   \n");
	ms_message("================================================   \n");
}

ortp_init();



	ms_message("================================================   \n");
	ms_message("=============sal_init初始化=================   \n");
	ms_message("================================================   \n");
	lc->sal=sal_init(lc->factory);
	
	

    ms_message("================================================   \n");
	ms_message("=============设置回调================   \n");
	ms_message("================================================   \n");
	sal_set_callbacks(lc->sal,&linphone_sal_callbacks);
	
	
	
     ms_message("================================================   \n");
    ms_message("=============初始化配置信息================   \n");
	ms_message("================================================   \n");

	remote_provisioning_uri = linphone_core_get_provisioning_uri(lc);


	if (remote_provisioning_uri == NULL) {
		linphone_configuring_terminated(lc, LinphoneConfiguringSkipped, NULL);
	} // else linphone_core_start will be called after the remote provisioning (see linphone_core_iterate)
	lc->bw_controller = ms_bandwidth_controller_new();	
	
	
	
```

linphone_core_init主要功能：


```
1.ortp_init();

2.ms_init();

3.lc->sal=sal_init();

4.初始化配置信息：

```

## ortp_init


## ms_init
## lc->sal=sal_init
## configure_init





