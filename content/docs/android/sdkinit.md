---
title: ����
sidebar: androidsidebar
secondnavandroid: true
---

## SDK��ʼ��{#init}

Ҫ����application������ʼ��

    EMChat.getInstance().init(applicationContext);
    
    ��    /**
         * debugMode == true ʱ��sdk ����log�����������Ϣ
         * @param debugMode
         * �������������ʱ����Ҫ���ó�false
         */
        EMChat.getInstance().setDebugMode(true);

#####ע��������app���е������ķ���������±ߵĴ��벢���ڳ�ʼ��SDK������ϱߣ���Ӧ����Ҳ�ɲο�demo��application��

	   appContext = this;
       int pid = android.os.Process.myPid();
       String processAppName = getAppName(pid);
       // ���app������Զ�̵�service����application:onCreate�ᱻ����2��
       // Ϊ�˷�ֹ����SDK����ʼ��2�Σ��Ӵ��жϻᱣ֤SDK����ʼ��1��
       // Ĭ�ϵ�app�����԰���ΪĬ�ϵ�process name�����У�����鵽��process name����app��process name����������
        
        if (processAppName == null ||!processAppName.equalsIgnoreCase("com.easemob.chatuidemo")) {
            Log.e(TAG, "enter the service process!");
            //"com.easemob.chatuidemo"Ϊdemo�İ����������Լ���Ŀ��Ҫ�ĳ��Լ�����
            
            // ���application::onCreate �Ǳ�service ���õģ�ֱ�ӷ���
            return;
        }

	private String getAppName(int pID) {
		String processName = null;
		ActivityManager am = (ActivityManager) this.getSystemService(ACTIVITY_SERVICE);
		List l = am.getRunningAppProcesses();
		Iterator i = l.iterator();
		PackageManager pm = this.getPackageManager();
		while (i.hasNext()) {
			ActivityManager.RunningAppProcessInfo info = (ActivityManager.RunningAppProcessInfo) (i.next());
			try {
				if (info.pid == pID) {
					CharSequence c = pm.getApplicationLabel(pm.getApplicationInfo(info.processName, PackageManager.GET_META_DATA));
					// Log.d("Process", "Id: "+ info.pid +" ProcessName: "+
					// info.processName +"  Label: "+c.toString());
					// processName = c.toString();
					processName = info.processName;
					return processName;
				}
			} catch (Exception e) {
				// Log.d("Process", "Error>> :"+ e.toString());
			}
		}
		return processName;
	}


##��¼��Ȩ{#login}

### ��½���������

<strong>��Ҫע�⣺</strong>
��½�ɹ�����Ҫ����<br/>
EMGroupManager.getInstance().loadAllGroups();<br/>
EMChatManager.getInstance().loadAllConversations();<br/>
��֤������ҳ��󱾵ػỰ��Ⱥ�鶼load��ϡ����������½����app�����ں�̨�ٽ���ʱ��Ҳ���ܻᵼ�¼��ص��ڴ��Ⱥ��ͻỰΪ�գ���������ҳ���oncreate��Ҳ������������룬��Ȼ�����õİ취Ӧ���Ƿ��ڳ���Ŀ���ҳ���ɲο�demo��SplashActivity��

<pre class="hll"><code class="language-java">

EMChatManager.getInstance().login(userName,password,
	new EMCallBack() {//�ص�
		@Override
		public void onSuccess() {
			runOnUiThread(new Runnable() {
				public void run() {
					Log.d("main", "��½����������ɹ���");		
				}
			});
		}

		@Override
		public void onProgress(int progress, String status) {

		}

		@Override
		public void onError(int code, String message) {
			Log.d("main", "��½���������ʧ�ܣ�");
		}
	});
</code></pre>

### �˳������½  {#logout}

<pre class="hll"><code class="language-java">
EMChatManager.getInstance().logout();//�˷���Ϊͬ������
����
EMChatManager.getInstance().logout(new EMCallBack(){})//�˷���Ϊ�첽����<br/>
//�����У�������new EMCallBack()��Ϊnew EMCallBack(){}

	
</code></pre>

### ע��  {#register}

��Demo��RegisterActivity��ע���û������Զ�תΪСд��ĸ�����Խ����û�������Сдע��(ǿ�ҽ��鿪����ͨ����̨����rest�ӿ�ȥע�ỷ��id���ͻ���ע�᷽�����ᳫʹ��)
	
<pre class="hll"><code class="language-java">
new Thread(new Runnable() {
    public void run() {
      try {
         // ����sdkע�᷽��
         EMChatManager.getInstance().createAccountOnServer(username, pwd);
      } catch (final Exception e) {
      //ע��ʧ��
		String errorMsg = e.getMessage();
		if(errorCode==EMError.NONETWORK_ERROR){
		    Toast.makeText(getApplicationContext(), "�����쳣���������磡", Toast.LENGTH_SHORT).show();
		}else if(errorCode==EMError.USER_ALREADY_EXISTS){
		    Toast.makeText(getApplicationContext(), "�û��Ѵ��ڣ�", Toast.LENGTH_SHORT).show();
		}else if(errorCode==EMError.UNAUTHORIZED){
			Toast.makeText(getApplicationContext(), "ע��ʧ�ܣ���Ȩ�ޣ�", Toast.LENGTH_SHORT).show();
		}else{
			Toast.makeText(getApplicationContext(), "ע��ʧ��: " + e.getMessage(), Toast.LENGTH_SHORT).show();
      }
   }
}).start();
</code></pre>

									




