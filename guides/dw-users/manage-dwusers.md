
# 管理数仓用户

本主题介绍如何邀请他人创建数仓用户、删除数仓用户、通知数仓用户重置密码、以及管理邀请。

## 概述

在 Relyt 中，数仓用户只能通过邀请创建或由账号扮演。只有由账号扮演的数仓用户有权限在对应数仓服务单元内进行数仓用户的管理操作。

## 邀请创建数仓用户

以下过程详细说明了如何邀请他人创建数仓用户。

1. 登录 Relyt 全局控制台，点击目标数仓服务单元卡片进入服务单元。

2. 在左侧导航栏中，选择 **访问控制** > **数仓用户**。

3. 在显示的页面上，点击右上角的 **+ 数仓用户**。

4. 在显示的对话框中，输入被邀请者的邮箱，然后点击 **确认**。

    一次只能输入一个邮箱。要发送多个邀请，重复步骤 4～5。

在被邀请者接受邀请并完成注册后，对应数仓用户信息可以在 **数仓用户** 页签上进行查看。

## 管理邀请

每当一个邀请创建成功后，都会生成一个邀请链接。该链接包含唯一标识此邀请的邀请 ID。你可以在对应数仓服务单元控制台上查看尚未接受、过期的邀请并进行管理。

1. 登录 Relyt 全局控制台，点击目标数仓服务单元卡片进入服务单元。

2. 在左侧导航栏中，选择 **访问控制** > **数仓用户**。

3. 点击 **邀请** 页签。

    只有处于 **等待接受中** 或 **已过期** 状态的邀请才会显示在列表中。

4. 在目标邀请的操作列中，选择 **....** > *要执行的操作*。

    支持的操作包括：

    - **复制邀请链接**：允许你直接将邀请链接粘贴给被邀请者，而不是通过电子邮件发送。

    - **重新发送邀请**：再次向被邀请者发送邀请。

    - **删除**：删除邀请。删除邀请后，对应邀请链接将失效。

## 删除数仓用户

你可以从你的数仓服务单元中删除不必要的数仓用户。

1. 登录 Relyt 全局控制台，点击目标数仓服务单元卡片进入服务单元。

2. 在左侧导航栏中，选择 **访问控制** > **数仓用户**。

3. 在默认展示的 **数仓用户** 页签中，找到需要删除的数仓用户，并在操作列中点击删除图标。

4. 在弹出的确认对话框中，输入要删除的数仓用户名，点击 **确认**。

删除数仓用户后，该用户将无法再访问当前数仓服务单元。

## 提醒数仓用户重置密码

当你认为某数仓用户有可能存在潜在的密码泄露风险，你可以提示该数仓用户重置密码。

:::note
在 Relyt 中，只有账号（包含账号和数仓用户）所有者有权修改自身的密码。
:::

1. 登录 Relyt 全局控制台，点击目标数仓服务单元卡片进入服务单元。

2. 在左侧导航栏中，选择 **访问控制** > **数仓用户**。

3. 在默认展示的 **数仓用户** 页签中，找到需要删除的数仓用户，并在操作列中点击重置密码图标。

完成操作后，Relyt 将邮件提醒目标数仓用户修改密码。
