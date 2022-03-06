```java

1. 这里上下级之间以pid来关联，pid是指上一级权限的id，顶级权限的id为0
	1.1 定义包含下级权限的对象
    	继承自UmsPermission对象，之增加了一个children属性，用于存储下级权限

		public class UmsPermissionNode extends UmsPermission {
    		private List<UmsPermissionNode> children;
    		public List<UmsPermissionNode> getChildren() {
        		return children;
    		}
    		public void setChildren(List<UmsPermissionNode> children) {
        		this.children = children;
    		}
		}
        
	1.2 定义获取树形结构的方法
    	我们先过滤出pid为0的顶级权限，然后给每个顶级权限设置其子级权限，covert方法的主要用途就是从所有权限中找出相应权限的子级权限。
        
        @Override
        public List<UmsPermissionNode> treeList() {
            List<UmsPermission> permissionList = permissionMapper.selectByExample(new UmsPermissionExample());
            List<UmsPermissionNode> result = permissionList.stream()
                    .filter(permission -> permission.getPid().equals(0L))
                    .map(permission -> covert(permission, permissionList)).collect(Collectors.toList());
            return result;
        }
        
    1.3 为每个权限设置子级权限
    	这里我们使用filter操作来过滤出每个权限的子级权限，由于子级权限下面可能还会有子级权限，这里我们使用递归来解决。
        但是递归操作什么时候停止，这里把递归调用方法放到了map操作中去，当没有子级权限时filter下的map操作便不会再执行，从而停止递归。
       
       /**
        * 将权限转换为带有子级的权限对象
        * 当找不到子级权限的时候map操作不会再递归调用covert
        */
        private UmsPermissionNode covert(UmsPermission permission, List<UmsPermission> permissionList) {
            UmsPermissionNode node = new UmsPermissionNode();
            BeanUtils.copyProperties(permission, node);
            List<UmsPermissionNode> children = permissionList.stream()
                   .filter(subPermission -> subPermission.getPid().equals(permission.getId()))
                   .map(subPermission -> covert(subPermission, permissionList)).collect(Collectors.toList());
            node.setChildren(children);
            return node;
        }

```