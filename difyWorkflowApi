import requests
import json
import os
 
class DifyAPIClient:
    """通用Dify API客户端"""
    
    def __init__(self, api_key, base_url="http://127.0.0.1", username=""):
        self.username = username
        self.api_key = api_key
        self.base_url = base_url
        self.headers = {
            'Authorization': f'Bearer {api_key}',
            'Content-Type': 'application/json'
        }
    
    def upload_file(self, file_path):
        """上传文件到Dify"""
        upload_url = f"{self.base_url}/v1/files/upload"
        
        with open(file_path, 'rb') as f:
            files = {'file': (os.path.basename(file_path), f, 'text/plain')}
            data = {'user': self.username}
            
            response = requests.post(upload_url, headers={'Authorization': f'Bearer {self.api_key}'}, 
                                   files=files, data=data, timeout=60)
        
        if response.status_code in [200, 201]:
            result = response.json()
            print(f"✅ 文件上传成功: {result['name']}")
            return result['id']
        else:
            print(f"❌ 文件上传失败: {response.status_code}")
            return None
    
    def run_workflow(self, workflow_inputs, file_id=None, timeout=300):
        """执行工作流"""
        workflow_url = f"{self.base_url}/v1/workflows/run"
        
        # 构建请求数据
        data = {
            "inputs": workflow_inputs,
            "response_mode": "blocking",
            "user": self.username
        }
        
        # 如果有文件，添加到inputs中
        if file_id:
            data["inputs"]["file"] = {
                "type": "document",
                "transfer_method": "local_file",
                "upload_file_id": file_id
            }
        
        print(f"🔄 调用工作流...")
        print(f"输入参数: {json.dumps(data['inputs'], ensure_ascii=False, indent=2)}")
        
        try:
            response = requests.post(workflow_url, headers=self.headers, 
                                   json=data, timeout=timeout)
            
            if response.status_code == 200:
                result = response.json()
                print("✅ 工作流执行成功！")
                return result.get('answer', result)
            else:
                print(f"❌ 工作流执行失败: {response.status_code}")
                print(f"错误信息: {response.text}")
                return None
                
        except requests.exceptions.Timeout:
            print("❌ 请求超时")
            return None
        except Exception as e:
            print(f"❌ 执行异常: {e}")
            return None
 
def main():
    """主函数 - 在这里修改你的配置"""
    
    # ========== 配置区域 ==========
    API_KEY = "YOUR_API_KEY"  # 请替换为你的API密钥
    BASE_URL = "http://127.0.0.1"          # Dify服务地址，根据你自己的地址和端口进行调整
    FILE_PATH = "./3001.txt"                  # 文件路径
    
    # 工作流输入参数 - 根据你的工作流配置修改
    WORKFLOW_INPUTS = {
        # "day": 10,                    # 示例参数1
        # "requirement": "请输入职位要求"      # 根据实际需求修改
        # 在这里添加更多参数...
    }
    
    # 是否使用文件
    USE_FILE = False                  # True: 上传文件, False: 不使用文件
    TIMEOUT = 300                     # 超时时间（秒）
    # ==============================
    
    # 创建客户端
    # 添加用户名配置
    USER_NAME = "YOUR_USERNAME"       # 请替换为你的用户名
    
    # 创建客户端
    client = DifyAPIClient(API_KEY, BASE_URL, USER_NAME)
    
    # 上传文件（如果需要）
    file_id = None
    if USE_FILE and FILE_PATH:
        if not os.path.exists(FILE_PATH):
            print(f"❌ 文件不存在: {FILE_PATH}")
            return
        
        print(f"📤 上传文件: {FILE_PATH}")
        file_id = client.upload_file(FILE_PATH)
        if file_id is None:
            return
    
    # 执行工作流
    print(f"🚀 执行工作流...")
    result = client.run_workflow(WORKFLOW_INPUTS, file_id, TIMEOUT)
    # 提取text内容（添加空值保护）
    result_text = result.get('data', {}).get('outputs', {}).get('text') if result else None
    
    # 输出结果
    if result:
        print("\n" + "="*60)
        print("执行结果:")
        print("="*60)
        print(result_text)
        print("="*60)
    else:
        print("❌ 执行失败")
 
if __name__ == "__main__":
    main()
