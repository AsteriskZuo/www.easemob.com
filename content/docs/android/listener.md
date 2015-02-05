---
title: ����
sidebar: androidsidebar
secondnavandroid: true
---

##�����¼�ע�ᣨ��������ȫ�ּ�����

####1.ע���������Ϣ�ļ����㲥    {#newmessage}

<pre class="hll"><code class="language-java">
//ֻ��ע���˹㲥���ܽ��յ�����Ϣ��Ŀǰ������Ϣ��������Ϣ�����߽�����Ϣ�Ĺ㲥��������ϢĿǰ�޷��������ڵ�¼�Ժ󣬽�����Ϣ�㲥��ִ��һ���õ����е�������Ϣ��
NewMessageBroadcastReceiver msgReceiver = new NewMessageBroadcastReceiver();
IntentFilter intentFilter = new IntentFilter(EMChatManager.getInstance().getNewMessageBroadcastAction());
intentFilter.setPriority(3);
registerReceiver(msgReceiver, intentFilter);

private class NewMessageBroadcastReceiver extends BroadcastReceiver {
	@Override
	public void onReceive(Context context, Intent intent) {
	    // ע���㲥
		abortBroadcast();

		// ��Ϣid��ÿ����Ϣ��������Ψһ��һ��id��Ŀǰ��SDK���ɣ�
		String msgId = intent.getStringExtra("msgid");
		//���ͷ�
		String username = intent.getStringExtra("from");
		// �յ�����㲥��ʱ��message�Ѿ���db���ڴ����ˣ�����ͨ��id��ȡmesage����
		EMMessage message = EMChatManager.getInstance().getMessage(msgId);
		EMConversation	conversation = EMChatManager.getInstance().getConversation(username);
		// �����Ⱥ����Ϣ����ȡ��group id
		if (message.getChatType() == ChatType.GroupChat) {
			username = message.getTo();
		}
		if (!username.equals(username)) {
			// ��Ϣ���Ƿ�����ǰ�Ự��return
			return;
		}
	}
}
	
</code></pre>

####2.ע�����ack��ִ��Ϣ��BroadcastReceiver    {#ackmessage}

<pre class="hll"><code class="language-java">
EMChatManager.getInstance().getChatOptions().setRequireAck(flag)
//����õ��Ѷ��Ļ�ִ��Ҫ�����flag���ó�true

IntentFilter ackMessageIntentFilter = new IntentFilter(EMChatManager.getInstance().getAckMessageBroadcastAction());
ackMessageIntentFilter.setPriority(3);
registerReceiver(ackMessageReceiver, ackMessageIntentFilter);

private BroadcastReceiver ackMessageReceiver = new BroadcastReceiver() {
	
	@Override
	public void onReceive(Context context, Intent intent) {
		abortBroadcast();
		String msgid = intent.getStringExtra("msgid");
		String from = intent.getStringExtra("from");
		EMConversation conversation = EMChatManager.getInstance().getConversation(from);
		if (conversation != null) {
			// ��message��Ϊ�Ѷ�
			EMMessage msg = conversation.getMessage(msgid);
			if (msg != null) {
				msg.isAcked = true;
			}
		}
				
	}
};
	
</code></pre>
		
####3.ע��һ����Ϣ�ʹ��BroadcastReceiver�����������ע�ᣩ    {#deliveryackmessage}

<pre class="hll"><code class="language-java">
EMChatManager.getInstance().getChatOptions().setRequireDeliveryAck(flag)
//����õ��ѷ��͵Ļ�ִ��Ҫ�����flag���ó�true
     
IntentFilter deliveryAckMessageIntentFilter = new IntentFilter(EMChatManager.getInstance().getDeliveryAckMessageBroadcastAction());
deliveryAckMessageIntentFilter.setPriority(5);
registerReceiver(deliveryAckMessageReceiver, deliveryAckMessageIntentFilter);
		
/**
* ��Ϣ�ʹ�BroadcastReceiver
*/
private BroadcastReceiver deliveryAckMessageReceiver = new BroadcastReceiver() {
    @Override
	public void onReceive(Context context, Intent intent) {
		abortBroadcast();

		String msgid = intent.getStringExtra("msgid");
		String from = intent.getStringExtra("from");
		EMConversation conversation = EMChatManager.getInstance().getConversation(from);
		if (conversation != null) {
			// ��message��Ϊ�Ѷ�
			EMMessage msg = conversation.getMessage(msgid);
			if (msg != null) {
				msg.isDelivered = true;
			}
		}
	}
};
	
</code></pre>


####4.������ϵ�˵ı仯��    {#contactlistener}

<pre class="hll"><code class="language-java">
EMContactManager.getInstance().setContactListener(new MyContactListener());

private class MyContactListener implements EMContactListener {

	@Override
	public void onContactAdded(List<String> usernameList) {
		// �������ӵ���ϵ��
			
	}

	@Override
	public void onContactDeleted(final List<String> usernameList) {
		// ��ɾ��

	}

	@Override
	public void onContactInvited(String username, String reason) {
		// �ӵ��������Ϣ�����������(ͬ���ܾ�)�����ߺ󣬷��������Զ��ٷ����������Կͻ��˲�Ҫ�ظ�����
			
	}

	@Override
	public void onContactAgreed(String username) {
		//ͬ���������
	}

	@Override
	public void onContactRefused(String username) {
		// �ܾ���������

	}

}
	
</code></pre>

####5.ע��һ����������״̬��listener    {#connectionlistener}

1.���������������������������⣬�ڴ�SDKΪ���ṩ����������ӿڣ�ʵʱ����

2.����ͬһ���˺��ڶദ��¼������ݱ������¼��е�_onDisConnected_�������ݵ�int���Ͳ���error�������ж��Ƿ�ͬһ���˺��������ط������˵�¼���˺��Ƿ�ɾ���������������صĲ���ֵΪ`EMError.CONNECTION_CONFLICT`������Ϊ����ͬһ���˺���ص�¼�������������صĲ���ֵΪ`EMError.USER_REMOVED`�������˺��ں�̨��ɾ��

<pre class="hll"><code class="language-java">
//ע��һ����������״̬��listener
EMChatManager.getInstance().addConnectionListener(new MyConnectionListener());

//ʵ��ConnectionListener�ӿ�
private class MyConnectionListener implements EMConnectionListener {
    @Override
	public void onConnected() {
	}
	@Override
	public void onDisconnected(final int error) {
		runOnUiThread(new Runnable() {

			@Override
			public void run() {
				if(error == EMError.USER_REMOVED){
					// ��ʾ�ʺ��Ѿ����Ƴ�
				}else if (error == EMError.CONNECTION_CONFLICT) {
					// ��ʾ�ʺ��������豸��½
				} else {
				if (NetUtils.hasNetwork(MainActivity.this))
					//���Ӳ������������
				else
					//��ǰ���粻���ã�������������
				}
			}
		});
	}
}
    
</code></pre>

####6.ע��Ⱥ����ص�listener    {#groupchangelistener}

<pre class="hll"><code class="language-java">
EMGroupManager.getInstance().addGroupChangeListener(new MyGroupChangeListener());

private class MyGroupChangeListener implements GroupChangeListener {

	@Override
	public void onInvitationReceived(String groupId, String groupName,String inviter, String reason) {

		//�յ�����Ⱥ�ĵ�����

		boolean hasGroup = false;
		for (EMGroup group : EMGroupManager.getInstance().getAllGroups()) {
			if (group.getGroupId().equals(groupId)) {
				hasGroup = true;
				break;
			}
		}
		if (!hasGroup)
			return;

		// ������
		EMMessage msg = EMMessage.createReceiveMessage(Type.TXT);
		msg.setChatType(ChatType.GroupChat);
		msg.setFrom(inviter);
		msg.setTo(groupId);
		msg.setMsgId(UUID.randomUUID().toString());
		msg.addBody(new TextMessageBody(inviter + "�����������Ⱥ��"));
		// ����������Ϣ
		EMChatManager.getInstance().saveMessage(msg);
		// ��������Ϣ
		EMNotifier.getInstance(getApplicationContext()).notifyOnNewMsg();

	}

	@Override
	public void onInvitationAccpted(String groupId, String inviter,
				String reason) {
			//Ⱥ�����뱻����
	}

	@Override
	public void onInvitationDeclined(String groupId, String invitee,
				String reason) {
		//Ⱥ�����뱻�ܾ�
	}

	@Override
	public void onUserRemoved(String groupId, String groupName) {
		//��ǰ�û�������Ա�Ƴ���Ⱥ��
			
	}

	@Override
	public void onGroupDestroy(String groupId, String groupName) {
		//Ⱥ�ı������߽�ɢ
		// ��ʾ�û�Ⱥ����ɢ

	}

	@Override
	public void onApplicationReceived(String groupId, String groupName,String applyer, String reason) {
		// �û��������Ⱥ�ģ��յ���Ⱥ����
	}

	@Override
	public void onApplicationAccept(String groupId, String groupName,String accepter) {
		// ��Ⱥ���뱻ͬ��
		EMMessage msg = EMMessage.createReceiveMessage(Type.TXT);
		msg.setChatType(ChatType.GroupChat);
		msg.setFrom(accepter);
		msg.setTo(groupId);
		msg.setMsgId(UUID.randomUUID().toString());
		msg.addBody(new TextMessageBody(accepter + "ͬ�������Ⱥ������"));
		// ����ͬ����Ϣ
		EMChatManager.getInstance().saveMessage(msg);
		// ��������Ϣ
		EMNotifier.getInstance(getApplicationContext()).notifyOnNewMsg();
	}

	@Override
	public void onApplicationDeclined(String groupId, String groupName,String decliner, String reason) {
		// ��Ⱥ���뱻�ܾ�
	}

}
	
</code></pre>

###ע�����Ҫ֪ͨsdk��UI �Ѿ���ʼ����ϣ�ע������Ӧ��receiver��listener, ���Խ���broadcast��    {#setappinit}

<pre class="hll"><code class="language-java">
EMChat.getInstance().setAppInited();
	
</code></pre>