gitimport os

# 确保文件存储目录存在
if not os.path.exists("todo_files"):
    os.makedirs("todo_files")

#加载事项
def load_todos():
    """从文件加载待办事项"""
    try:
        with open("todos.txt", "r", encoding="utf-8") as f:
            todos = []
            for line in f.readlines():
                line = line.strip()
                if line:
                    # 格式: 状态,内容,标签,文件名(可选)
                    parts = line.split(",", 3)
                    # 处理可能没有文件的情况
                    file_name = parts[3] if len(parts) > 3 else ""
                    todos.append({
                        "status": int(parts[0]),
                        "content": parts[1],
                        "tag": parts[2],
                        "file": file_name
                    })
            return todos
    except FileNotFoundError:
        return []

#保存事项
def save_todos(todos):
    """保存待办事项到文件"""
    with open("todos.txt", "w", encoding="utf-8") as f:
        for todo in todos:
            line = f"{todo['status']},{todo['content']},{todo['tag']},{todo['file']}\n"
            f.write(line)

#标签函数
def get_all_tags(todos):
    """获取所有不重复的标签"""
    tags = set()
    for todo in todos:
        if todo['tag']:
            tags.add(todo['tag'])
    return sorted(tags)


def filter_todos_by_tag(todos, tag):
    """根据标签筛选待办事项"""
    if not tag:
        return todos
    return [todo for todo in todos if todo['tag'] == tag]


# 搜索功能
def search_todos(todos, keyword):
    """根据关键词模糊匹配匹配待办事项"""
    # 转换为小写，实现不区分大小写的搜索
    keyword = keyword.lower()
    # 筛选出内容或标签中包含关键词的事项
    return [
        todo for todo in todos
        # 检查内容是否包含关键词
        if keyword in todo['content'].lower()
           # 或者标签是否包含关键词
           or keyword in todo['tag'].lower()
    ]

#文件上传
def upload_file(todo_index):
    """为指定待办事项上传文件（流式处理，支持大文件）"""
    todos = load_todos()

    if 0 <= todo_index < len(todos):
        file_path = input("请输入要上传的文件路径: ").strip()

        if not os.path.exists(file_path):
            print("文件不存在!")
            return

        file_name = os.path.basename(file_path)
        save_path = os.path.join("todo_files", file_name)

        try:
            with open(file_path, "rb") as src, open(save_path, "wb") as dst:
                chunk_size = 1024 * 1024
                while True:
                    chunk = src.read(chunk_size)
                    if not chunk:
                        break
                    dst.write(chunk)

            todos[todo_index]["file"] = file_name
            save_todos(todos)
            print(f"文件上传成功，保存为: {file_name}")

        except Exception as e:
            print(f"上传失败: {str(e)}")
    else:
        print("无效的待办事项序号")

#添加待办
def add_todo():
    """添加新的待办事项"""
    todos = load_todos()
    content = input("请输入待办事项内容: ")
    tag = input("请输入标签(可选): ") or "无"

    todos.append({
        "status": 0,
        "content": content,
        "tag": tag,
        "file": ""
    })

    save_todos(todos)
    print("添加成功!")

#显示待办
def show_todos(todos=None, title="所有待办事项"):
    """显示待办事项，支持自定义列表和标题"""
    # 如果没有传入特定列表，就加载所有
    if todos is None:
        todos = load_todos()

    print(f"\n===== {title} =====")

    if not todos:
        print("没有符合条件的待办事项")
        return

    for i, todo in enumerate(todos, 1):
        status = "√" if todo["status"] == 1 else "×"
        file_info = f"[有文件: {todo['file']}]" if todo["file"] else ""
        print(f"{i}. [{status}] {todo['content']} (标签: {todo['tag']}) {file_info}")
    print("=======================\n")

#标记完成
def complete_todo():
    """标记待办事项为已完成"""
    todos = load_todos()
    if not todos:
        print("没有待办事项")
        return

    show_todos()
    try:
        index = int(input("请输入要标记完成的序号: ")) - 1
        if 0 <= index < len(todos):
            todos[index]["status"] = 1
            save_todos(todos)
            print("标记成功!")
        else:
            print("序号不正确")
    except ValueError:
        print("请输入数字")

#删除事项
def delete_todo():
    """删除待办事项（包括关联文件）"""
    todos = load_todos()
    if not todos:
        print("没有待办事项")
        return

    show_todos()
    try:
        index = int(input("请输入要删除的序号: ")) - 1
        if 0 <= index < len(todos):
            file_name = todos[index]["file"]
            if file_name and os.path.exists(os.path.join("todo_files", file_name)):
                os.remove(os.path.join("todo_files", file_name))
                print(f"已删除关联文件: {file_name}")

            del todos[index]
            save_todos(todos)
            print("删除成功!")
        else:
            print("序号不正确")
    except ValueError:
        print("请输入数字")

#标签筛选
def filter_by_tag_menu():
    """标签筛选菜单"""
    todos = load_todos()
    if not todos:
        print("没有待办事项")
        return

    tags = get_all_tags(todos)
    print("可用标签:")
    for i, tag in enumerate(tags, 1):
        print(f"{i}. {tag}")
    print(f"{len(tags) + 1}. 取消筛选（显示全部）")

    try:
        choice = int(input("请选择要筛选的标签序号: "))
        if 1 <= choice <= len(tags):
            filtered = filter_todos_by_tag(todos, tags[choice - 1])
            show_todos(filtered, f"标签筛选: {tags[choice - 1]}")
        elif choice == len(tags) + 1:
            show_todos()
        else:
            print("无效的序号")
    except ValueError:
        print("请输入数字")


# 搜索功能交互菜单
def search_todos_menu():
    """搜索事项的交互菜单"""
    todos = load_todos()
    if not todos:
        print("没有待办事项")
        return

    # 获取用户输入的搜索关键词
    keyword = input("请输入搜索关键词: ").strip()
    if not keyword:  # 如果用户输入空
        print("请输入有效的搜索关键词")
        return

    # 调用搜索函数获取结果
    results = search_todos(todos, keyword)
    # 显示搜索结果，标题中包含关键词
    show_todos(results, f"搜索结果: '{keyword}'")

#主函数
def main():
    """主函数，程序入口"""
    print("欢迎使用带文件上传的待办事项管理器")
    while True:
        print("\n请选择操作:")
        print("1. 添加待办事项")
        print("2. 查看所有事项")
        print("3. 按标签筛选事项")
        print("4. 搜索事项")  #
        print("5. 标记事项为完成")
        print("6. 为事项上传文件")
        print("7. 删除事项")
        print("8. 退出程序")

        choice = input("请输入选项(1-8): ")

        if choice == "1":
            add_todo()
        elif choice == "2":
            show_todos()
        elif choice == "3":
            filter_by_tag_menu()
        elif choice == "4":
            search_todos_menu()
        elif choice == "5":
            complete_todo()
        elif choice == "6":
            show_todos()
            try:
                index = int(input("请输入要上传文件的事项序号: ")) - 1
                upload_file(index)
            except ValueError:
                print("请输入数字")
        elif choice == "7":
            delete_todo()
        elif choice == "8":
            print("谢谢使用，再见!")
            break
        else:
            print("无效选项，请重新输入")


if __name__ == "__main__":
    main()

