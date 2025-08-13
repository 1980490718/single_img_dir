定制板级配置说明
[1] 镜像创建:
 要使用 config/ 目录中提供的配置创建镜像，请使用命令：
 前提是 partition_tool、nand_mbn_generator.py、cdt_mod.py、smem_tool.py、cb-cdt.mbn 和 config 目录都存在于当前目录中。 
 将镜像名称 "NAND_IMAGES" 替换为要生成的所需镜像名称。 
 可用的镜像列表有 NAND_IMAGES、NOR_IMAGES、EMMC_IMAGES、NOR_PLUS_NAND_IMAGES、SMEM_IMAGES、CDT_IMAGES。 
 如果移动了列出的工具，则在参数中给出相应路径（例如 --partition_tool=/tmp/partition_tool）。 
 默认情况下，输出创建在名为 out/ 的目录中，可以通过添加另一个参数 --outdir 来修改（例如 --outdir=/my/path/out）。
 python genimg.py --partition_tool=partition_tool --mbn_gen=nand_mbn_generator.py --cdt_mod=cdt_mod.py --smem_gen=smem-tool.py --configdir=config/ -- skip_export --cdt_bin=cb-cdt.mbn --cdt_modxml=config/cb-cdtmod.xml --cdt_outbin=cb-cdtnew.mbn --image_name=NAND_IMAGES

注意：脚本需要文件 boardconfig，因此在不更改脚本的情况下无法重命名它。但是其他文件可以重命名（虽然不推荐），在这种情况下，它应该反映在 boardconfig 文件中。

[2] 分区表自定义: 
 要自定义分区表，需要编辑相应闪存的 XML。 例如，对于 NAND 启动，编辑文件 config/nand-partition.xml 并运行 [1] 中给出的命令。
 [2.0] 要添加新条目，请复制现有分区条目并根据以下规则进行修改
 
 [2.1] 在分区 XML 中，大小可以指定为 size_kb 或 size_block 选项。
	如果指定了 size_kb，则最后一个属性应为 0xFF，而 size_block 应将最后一个属性指定为 0xFE

 [2.2] 如果 size_kb 选项指定为 0xFFFFFFFF，则将其视为增长分区，即所有剩余空间都用于该特定分区。
	这使得只有最后一个条目应指定为增长分区大小

 [2.3] 分区 0:SBL1 不能重新排序，它应该始终是第一个条目

[3] DDR 参数自定义: 要自定义 DDR 参数，请编辑 XML 文件 config/cb-cdtmod.xml 并运行 [1] 中给出的命令。
 DDR 的自定义可以通过 config/cb-cdtmod.xml 中给出的值来实现。 这些值被获取并修补到现有的 cdt.mbn 二进制文件中。 
 行数、列数和 bank 数应与所使用的 DDR 匹配，并且可以从 DDR 数据表中获得。 如果这些值不匹配，则会导致启动失败。 使用给定的 XML 文件仅支持 DDR3。
 
[4] 机器 ID 自定义:
 必须修改机器ID，因为整个系统的启动和初始化流程都依赖此ID。
 完成应用程序引导程序（bootloader）和高层级操作系统（HLOS）中机器ID的必要更改后，需将新机器ID填入以下文件：
 * config/boardconfig
 * config/cb-cdtmod.xml
