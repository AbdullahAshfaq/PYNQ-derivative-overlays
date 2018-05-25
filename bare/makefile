overlay_name := bare
design_name := system
base_path := ../base
device := xc7z020clg400-1
arch := arm

all: base tcl bit dsa sdx_platform
	@echo
	@tput setaf 2 ; echo "Built $(overlay_name) successfully!"; tput sgr0;
	@echo

base:
	@if [ ! -d "./base" ]; then \
		pushd $(base_path); \
		if [ ! -d "./base" ]; then \
			echo "Rebuilding base..."; make; \
		else \
			echo "Skipping base..."; \
		fi; \
		popd; \
		cp -rf $(base_path)/base base; \
	fi

tcl:
	vivado -mode batch -source \
	build_$(overlay_name)_$(design_name).tcl -notrace

bit:
	@sed -i "s/\(create_project \)\(.*\)\( -part \)\(.*\)"\
	"/\1$(overlay_name) $(overlay_name)\3$(device)/" \
	$(overlay_name).tcl; \
	sed -i '/# CHANGE DESIGN NAME HERE/i \
	set_property  ip_repo_paths ../../ip [current_project]\
	update_ip_catalog\
	' $(overlay_name).tcl; \
	vivado -mode batch -source \
	$(overlay_name).tcl build_bitstream.tcl -notrace

dsa:
	vivado -mode batch -source build_$(overlay_name)_dsa.tcl -notrace
	unzip -qo $(overlay_name).dsa -d .dsa
	cp -rf .dsa/$(design_name).hwh ./$(overlay_name).hwh

sdx_platform:
	@if [ -d "../sdx_platform" ]; then \
		pushd ../sdx_platform; \
		echo "Building SDx platform..."; \
		make OVERLAY=$(overlay_name) ARCH=$(arch); \
		popd; \
	else \
		echo "Skipping SDx platform..."; \
	fi

clean:
	rm -rf $(overlay_name) *.jou *.log
	rm -rf base
	rm -rf .dsa