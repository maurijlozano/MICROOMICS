# Galaxy - MicroOmics

FROM bgruening/galaxy-stable
MAINTAINER Mauricio J. Lozano - email:mjlozano@biol.unlp.edu.ar


ENV GALAXY_CONFIG_BRAND="MicroOmics"

# Install MicroOmics tools
ADD microomics.yml $GALAXY_ROOT/tools.yaml
RUN install-tools $GALAXY_ROOT/tools.yaml && \
    /tool_deps/_conda/bin/conda clean --all --yes

# Container Style for Sniplay/Southgreen
ADD welcome_image.png $GALAXY_CONFIG_DIR/web/welcome_image.png
ADD welcome.html $GALAXY_CONFIG_DIR/web/welcome.html
