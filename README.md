# umeng-specs

友盟 iOS SDK 官方公开 CocoaPods Specs 仓库。

本仓库用于在 CocoaPods Trunk 转为只读后继续提供 SDK 版本，目前支持 UMCommon。SDK 二进制仍通过原有下载地址分发。

## 当前支持

- **UMCommon**：包含历史版本 Podspec，包括 7.7.0。
- **UMDevice**：UMCommon 的依赖，目前仍从 CocoaPods 公共源获取，因此接入时需要保留公共源。

未添加本仓库的工程仍可以使用公共源中的已有版本，前提是对应 SDK 下载地址持续有效；仅发布在本仓库的新版本需要通过本仓库获取。

## 接入方式

### 1. 配置 Podfile

在现有 Podfile 顶部添加本仓库，并保留公共源：

```ruby
source 'https://github.com/umeng/umeng-specs.git'
source 'https://cdn.cocoapods.org/'
```

在应用 target 中声明 UMCommon，例如：

```ruby
target 'YourApp' do
  pod 'UMCommon', '~> 7.7.0'
end
```

`YourApp` 和版本约束应按实际工程修改；保留工程原有的平台设置及其他依赖。

**源顺序很重要**：CocoaPods 默认优先从第一个包含该 Pod 的源选择版本，不会合并不同源的同名 Pod 版本。请将本仓库放在公共源之前。

### 2. 安装或升级

在工程目录执行：

```bash
pod install --repo-update
```

已有工程需要升级时，先调整 Podfile 中的版本约束，再执行：

```bash
pod update UMCommon
```

`pod install` 通常遵循锁文件中的已有版本，不会主动升级所有依赖。不需要手动执行 `pod repo add`，CocoaPods 会根据 Podfile 的 `source` 自动管理 Specs 源。

### 3. 确认使用了本仓库

安装成功后，检查 `Podfile.lock` 的 `SPEC REPOS`，UMCommon 应归属于本仓库：

```yaml
SPEC REPOS:
  https://github.com/umeng/umeng-specs.git:
    - UMCommon
```

UMDevice 或其他依赖仍归属于公共源属于正常情况。详细日志中的 `CDN: trunk` 表示读取了公共源，单凭这些日志不能判断 UMCommon 最终使用哪个源，应结合锁文件确认。

如需明确限定 UMCommon 的来源，可将原有 UMCommon 声明替换为：

```ruby
pod 'UMCommon', '7.7.0',
    :source => 'https://github.com/umeng/umeng-specs.git'
```
